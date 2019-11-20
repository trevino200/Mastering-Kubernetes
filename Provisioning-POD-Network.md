# Provisioning the Pod Network

Kubernetes use a CNI or Container Network Interface to deploy a network overlay between all the nodes. There are many CNI flavors like flannel, Calico and Weave. For this lab, we will use the weave CNI - [weave](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/) as our networking option.

### Install CNI plugins

Download the CNI Plugins required for weave on each of the worker nodes - `minion-1` and `minion-2`

`wget https://github.com/containernetworking/plugins/releases/download/v0.7.5/cni-plugins-amd64-v0.7.5.tgz`

Extract it to /opt/cni/bin directory

`sudo tar -xzvf cni-plugins-amd64-v0.7.5.tgz  --directory /opt/cni/bin/`

### Deploy Weave Network

Deploy weave network. Run only once on the `master` node.


`kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"`

Weave uses POD CIDR of `10.32.0.0/12` by default.

## Verification

List the registered Kubernetes nodes from the master node. we have to issue the command in the kube-system namespace with the -n kube-system.

```
master-1$ kubectl get pods -n kube-system
```

> output

```

```

Next: [Using RBAC in the Kubernetes cluster](RBAC.md)
