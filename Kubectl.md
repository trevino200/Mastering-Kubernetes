# Configuring kubectl for Remote Administration
Kubectl is used to manage your Kubernetes cluster and learning all the options for the kubectl will be key to administer the cluster.
Kubectl will have to be trusted and authenticated with the API-server and we will generate a kubeconfig file for the `kubectl` command line utility based on the `admin` user credentials.

> Run the commands in this lab from the same directory used to generate the admin client certificates as it uses the certicates we generated in the previous sections

## The Admin Kubernetes Configuration File

Each kubeconfig requires a Kubernetes API Server to connect to. To support high availability the IP address assigned to the external load balancer fronting the Kubernetes API Servers will be used.

Generate a kubeconfig file suitable for authenticating as the `admin` user:

```
{
  KUBERNETES_LB_ADDRESS=192.168.50.30

  kubectl config set-cluster Mastering-Kubernetes \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${KUBERNETES_LB_ADDRESS}:6443

  kubectl config set-credentials admin \
    --client-certificate=admin.crt \
    --client-key=admin.key

  kubectl config set-context Mastering-Kubernetes \
    --cluster=Mastering-Kubernetes \
    --user=admin

  kubectl config use-context Mastering-Kubernetes
}
```

## Verification

Check the health of the remote Kubernetes cluster:

```
kubectl get componentstatuses
```

> output

```
NAME                 STATUS    MESSAGE             ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-1               Healthy   {"health":"true"}
etcd-0               Healthy   {"health":"true"}
```

List the nodes in the Kubernetes cluster :

```
kubectl get nodes
```

> output

```
NAME       STATUS   ROLES    AGE    VERSION
minion-1   NotReady    <none>   118s   v1.13.0
minion-2   NotReady    <none>   118s   v1.13.0
```
Note: At this stage the worker node are in a `NotReady` state as we have not configured the networking. Once that is done, they will change to 'Ready' state.

Next: [Configure the Pod Networking](Provisioning-POD-Network.md)
