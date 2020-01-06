# Bootstrapping the Kubernetes Worker Nodes

In this lab you will bootstrap 2 Kubernetes worker nodes. We already have [Docker](https://www.docker.com) installed on these nodes.

We will now install the kubernetes components
- [kubelet](https://kubernetes.io/docs/admin/kubelet)
- [kube-proxy](https://kubernetes.io/docs/concepts/cluster-administration/proxies).

## Prerequisites

The commands in this lab must be run on first worker instance: `minion-1`. Login to first worker node instance using SSH Terminal.

### Provisioning  Kubelet Client Certificates

Kubernetes uses a [special-purpose authorization mode](https://kubernetes.io/docs/admin/authorization/node/) called Node Authorizer, that specifically authorizes API requests made by [Kubelets](https://kubernetes.io/docs/concepts/overview/components/#kubelet). In order to be authorized by the Node Authorizer, Kubelets must use a credential that identifies them as being in the `system:nodes` group, with a username of `system:node:<nodeName>`. In this section you will create a certificate for each Kubernetes worker node that meets the Node Authorizer requirements.

On the master node ie <master-1>, Generate a certificate and private key for each of the worker nodes:

For minion-1
```
master-1$ cat > openssl-minion-1.cnf <<EOF
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
[req_distinguished_name]
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = minion-1
IP.1 = 192.168.50.201
EOF

openssl genrsa -out minion-1.key 2048
openssl req -new -key minion-1.key -subj "/CN=system:node:minion-1/O=system:nodes" -out minion-1.csr -config openssl-minion-1.cnf
openssl x509 -req -in minion-1.csr -CA ca.crt -CAkey ca.key -CAcreateserial  -out minion-1.crt -extensions v3_req -extfile openssl-minion-1.cnf -days 1000
```

Results:

```
minion-1.key
minion-1.crt
```

For minion-2:

```
master-1$ cat > openssl-minion-2.cnf <<EOF
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
[req_distinguished_name]
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = minion-2
IP.1 = 192.168.50.202
EOF

openssl genrsa -out minion-2.key 2048
openssl req -new -key minion-2.key -subj "/CN=system:node:minion-2/O=system:nodes" -out minion-2.csr -config openssl-minion-2.cnf
openssl x509 -req -in minion-2.csr -CA ca.crt -CAkey ca.key -CAcreateserial  -out minion-2.crt -extensions v3_req -extfile openssl-minion-2.cnf -days 1000
```

Results:

```
minion-2.key
minion-2.crt
```
### The kubelet Kubernetes Configuration File

When generating kubeconfig files for Kubelets the client certificate matching the Kubelet's node name must be used. This will ensure Kubelets are properly authorized by the Kubernetes [Node Authorizer](https://kubernetes.io/docs/admin/authorization/node/).

Get the kub-api server load-balancer IP.
```
LOADBALANCER_ADDRESS=192.168.50.30
```

Generate a kubeconfig file for the first worker node:

```
{
  kubectl config set-cluster Mastering-Kubernetes \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${LOADBALANCER_ADDRESS}:6443 \
    --kubeconfig=minion-1.kubeconfig

  kubectl config set-credentials system:node:minion-1 \
    --client-certificate=minion-1.crt \
    --client-key=minion-1.key \
    --embed-certs=true \
    --kubeconfig=minion-1.kubeconfig

  kubectl config set-context default \
    --cluster=Mastering-Kubernetes \
    --user=system:node:minion-1 \
    --kubeconfig=minion-1.kubeconfig

  kubectl config use-context default --kubeconfig=minion-1.kubeconfig
}
```

Results:

```
minion-1.kubeconfig
```

```
{
  kubectl config set-cluster Mastering-Kubernetes \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${LOADBALANCER_ADDRESS}:6443 \
    --kubeconfig=minion-2.kubeconfig

  kubectl config set-credentials system:node:minion-2 \
    --client-certificate=minion-2.crt \
    --client-key=minion-2.key \
    --embed-certs=true \
    --kubeconfig=minion-2.kubeconfig

  kubectl config set-context default \
    --cluster=Mastering-Kubernetes \
    --user=system:node:minion-2 \
    --kubeconfig=minion-2.kubeconfig

  kubectl config use-context default --kubeconfig=minion-2.kubeconfig
}
```

Results:

```
minion-2.kubeconfig
```

### Copy certificates, private keys and kubeconfig files to the worker node:

```
master-1$ scp ca.crt minion-1.crt minion-1.key minion-1.kubeconfig minion-1:~/
master-1$ scp ca.crt minion-2.crt minion-2.key minion-2.kubeconfig minion-2:~/
```

### Download and Install Worker Binaries on EACH of the worker nodes.. for this lab, minion-1 and minion-2


```
minion-1$ wget -q --show-progress --https-only --timestamping \
  https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubectl \
  https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy \
  https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet
```

Create the installation directories:

```
minion-1$ sudo mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes
```

Install the worker binaries:

```
{
  chmod +x kubectl kube-proxy kubelet
  sudo mv kubectl kube-proxy kubelet /usr/local/bin/
}
```

### Configure the Kubelet

```
{
  sudo mv ${HOSTNAME}.key ${HOSTNAME}.crt /var/lib/kubelet/
  sudo mv ${HOSTNAME}.kubeconfig /var/lib/kubelet/kubeconfig
  sudo mv ca.crt /var/lib/kubernetes/
}
```

Create the `kubelet-config.yaml` configuration file:

```
minion-1$ cat <<EOF | sudo tee /var/lib/kubelet/kubelet-config.yaml
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    enabled: true
  x509:
    clientCAFile: "/var/lib/kubernetes/ca.crt"
authorization:
  mode: Webhook
clusterDomain: "cluster.local"
clusterDNS:
  - "10.96.0.10"
resolvConf: "/run/systemd/resolve/resolv.conf"
runtimeRequestTimeout: "15m"
EOF
```

> The `resolvConf` configuration is used to avoid loops when using CoreDNS for service discovery on systems running `systemd-resolved`.

Create the `kubelet.service` systemd unit file:

```
minion-1$ cat <<EOF | sudo tee /etc/systemd/system/kubelet.service
[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/kubernetes/kubernetes
After=docker.service
Requires=docker.service

[Service]
ExecStart=/usr/local/bin/kubelet \\
  --config=/var/lib/kubelet/kubelet-config.yaml \\
  --image-pull-progress-deadline=2m \\
  --kubeconfig=/var/lib/kubelet/kubeconfig \\
  --tls-cert-file=/var/lib/kubelet/${HOSTNAME}.crt \\
  --tls-private-key-file=/var/lib/kubelet/${HOSTNAME}.key \\
  --network-plugin=cni \\
  --register-node=true \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### Configure the Kubernetes Proxy

```
minion-1$ sudo mv kube-proxy.kubeconfig /var/lib/kube-proxy/kubeconfig
```

Create the `kube-proxy-config.yaml` configuration file:

```
minion-1$ cat <<EOF | sudo tee /var/lib/kube-proxy/kube-proxy-config.yaml
kind: KubeProxyConfiguration
apiVersion: kubeproxy.config.k8s.io/v1alpha1
clientConnection:
  kubeconfig: "/var/lib/kube-proxy/kubeconfig"
mode: "iptables"
clusterCIDR: "192.168.50.0/24"
EOF
```

Create the `kube-proxy.service` systemd unit file:

```
minion-1$ cat <<EOF | sudo tee /etc/systemd/system/kube-proxy.service
[Unit]
Description=Kubernetes Kube Proxy
Documentation=https://github.com/kubernetes/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-proxy \\
  --config=/var/lib/kube-proxy/kube-proxy-config.yaml
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### Start the Worker Services

```
{
  sudo systemctl daemon-reload
  sudo systemctl enable kubelet kube-proxy
  sudo systemctl start kubelet kube-proxy
}
```

ONCE AGAIN PLEASE RUN ALL THESE COMMAND ON EACH OF THE WORKER NODES...

## Verification

Verifying that the kubelet and kube-proxy services are active and running. This will indicates that each worker node has successfully authenticated and communicating with the master node cluster.

On any of the master nodes
```
kubectl get nodes 
```

> output

```

```

> The worker node to be in a NotReady state because we haven't configured Networking yet and which we will do next..

Next: [Configuring kubectl for remote management](Kubectl.md)
