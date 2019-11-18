# Mastering Kubernetes

Kubernetes or K8s is an open source container orchestration engine for automating the configuration, deployement and scaling of containers at large scale. The best way to truly master K8s is setup a cluster on your laptop, deploy pods with containers and test every possible option to scale and manage them.

The lab in this page will take you step by step for the configuration of a kubernetes cluster. There will be 2 variant..one lab to deploy a cluster using kubeadmin which is the easiest way and then creating a kubernetes cluster from scratch which would allow to truly master and troubleshoot kubernetes.

The lab can be run on a windows or mac laptop. We will be running the kubernetes cluster on ubuntu linux using VMs on Virtualbox. we will automate the provisioning of the VMs using vagrant but if you want to configure them manually that is fine as well.
The Ubuntu version for the lab is 16.04

The first step to learn Kubernetes is to create your cluster on your laptop as it will allow to undertand the various processes and get acquinted with how the master orchestrate containers on the worker nodes. 

The easiest way to quickly provision a 3 nodes K8s cluster is with KUBEADM and we will do that first.

STEP 1 >>PROVISIONING 3 VMs FOR THE KUBERNETES CLUSTER
##########################################################

First please install virtualbox, vagrant and git on your laptop

VirtualBox: https://www.virtualbox.org/wiki/Downloads

Vagrant:  https://www.vagrantup.com/downloads.html

Git:    https://git-scm.com/  



clone the repo on your laptop using git
> git clone https://github.com/dean-houari/Mastering-Kubernetes.git

Launch environments with Vagrant on your laptop console or terminal:

For windows user, get putty or Conem as terminal to manage your cluster:
https://www.puttygen.com/download-putty
https://www.fosshub.com/ConEmu.html


> cd K8s
 
> vagrant up
 
Login to all the nodes by launching three different consoles or terminals for each VM from VirtualBox to login to the K8s nodes. One will be the master and the 2 others will be the worker nodes where the pods with containers will be launched and managed.
Note: vagrant will download the ubuntu/xenial images when starting up and it may take some time depending on your network download speed.


...
C:\Users\dhouari\Mastering-Kubernetes\K8s>vagrant up
Bringing machine 'Master' up with 'virtualbox' provider...
Bringing machine 'Slave-1' up with 'virtualbox' provider...
Bringing machine 'Slave-2' up with 'virtualbox' provider...
==> Master: Box 'ubuntu/xenial64' could not be found. Attempting to find and install...
    Master: Box Provider: virtualbox
    Master: Box Version: >= 0
==> Master: Loading metadata for box 'ubuntu/xenial64'
    Master: URL: https://vagrantcloud.com/ubuntu/xenial64
==> Master: Adding box 'ubuntu/xenial64' (v20191114.0.0) for provider: virtualbox
    Master: Downloading: https://vagrantcloud.com/ubuntu/boxes/xenial64/versions/20191114.0.0/providers/virtualbox.box
    Master: Download redirected to host: cloud-images.ubuntu.com
    Master: Progress: 13% (Rate: 353k/s, Estimated time remaining: 0:14:45)
...

If any of the nodes failed to provision or is not configured correct, please delete the node using the command:

> vagrant destroy or > vagrant destroy <node>
 
then start again:

> vagrant up


On the first terminal:

> vagrant ssh Master

> sudo su
 
On the second terminal:

> vagrant ssh Slave-1

> sudo su

On the third terminal:

> vagrant ssh Slave-2

> sudo su


STEP 2>> CONFIGURING AND BOOTSTRAPING YOUR KUBERNETES CLUSTER
#############################################################

### Create Kubernetes Repository

We need to create a repository to download Kubernetes.

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
```
```
cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```


### Installation of the packages

We should update the machines before installing so that we can update the repository.
```
apt-get update -y
```
Installing all the required packages with dependencies:
```
apt-get install -y docker.io kubelet kubeadm kubectl kubernetes-cni
```
```
rm -rf /var/lib/kubelet/*
```

### Setup sysctl configs

In order for many container networks to work, the following needs to be enabled on each node.

```
sysctl net.bridge.bridge-nf-call-iptables=1
```
The above steps has to be followed in all the nodes.



## Initializing Master

This tutorial assumes "Master" host as the K8s cluster master and used kubeadm as a tool to install and setup the cluster. This section also assumes that you are using vagrant based setup provided along with this tutorial. If not, please update the IP address of the master accordingly.

To initialize master, run this on kube-01

```
kubeadm init --apiserver-advertise-address 192.168.12.10 --pod-network-cidr=192.168.0.0/16

```
### Initialization of the Nodes (Previously Minions)

After master being initialized, it should display the command which could be used on all worker/nodes to join the k8s cluster.

e.g.
```
kubeadm join --token c04797.8db60f6b2c0dd078 192.168.12.10:6443 --discovery-token-ca-cert-hash sha256:88ebb5d5f7fdfcbbc3cde98690b1dea9d0f96de4a7e6bf69198172debca74cd0
```


Copy and paste it on all node.

##### Troubleshooting Tips
If you lose  the join token, you could retrieve it using

```
kubeadm token list
```

On successfully joining the master, you should see output similar to following,

```
root@kube-03:~# kubeadm join --token c04797.8db60f6b2c0dd078 159.203.170.84:6443 --discovery-token-ca-cert-hash sha256:88ebb5d5f7fdfcbbc3cde98690b1dea9d0f96de4a7e6bf69198172debca74cd0
[kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
[preflight] Running pre-flight checks
[discovery] Trying to connect to API Server "159.203.170.84:6443"
[discovery] Created cluster-info discovery client, requesting info from "https://159.203.170.84:6443"
[discovery] Requesting info from "https://159.203.170.84:6443" again to validate TLS against the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "159.203.170.84:6443"
[discovery] Successfully established connection with API Server "159.203.170.84:6443"
[bootstrap] Detected server version: v1.8.2
[bootstrap] The server supports the Certificates API (certificates.k8s.io/v1beta1)

Node join complete:
* Certificate signing request sent to master and response
  received.
* Kubelet informed of new secure connection details.

Run 'kubectl get nodes' on the master to see this machine join.
```



### Setup the admin client - Kubectl - to manage the cluster

On Master Node
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
## Installing CNI with WeaveNet - we will use weave as it is my favorite ;) but widely used container overlay..you can use any other overlay of your choice at this stage. ..flannel, calico etc...

Installing overlay network is necessary for the pods to communicate with each other across the hosts. It is necessary to do this before you try to deploy any applications to your cluster.

There are various overlay networking drivers available for kubernetes. We are going to use **Weave Net**.

```

export kubever=$(kubectl version | base64 | tr -d '\n')
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$kubever"
```



## Validating the Setup

You could validate the status of this cluster, health of pods and whether all the components are up or not by using a few or all of the following commands.

To check if nodes are ready

```
kubectl get nodes

```

[ Expected output ]

```
root@kube-01:~# kubectl get nodes
NAME      STATUS    ROLES     AGE       VERSION
Master   Ready      master    15m        v1.8.2
Slave-1   Ready     <none>    10m        v1.8.2
Slave-2   Ready     <none>    10m        v1.8.2
```


Additional Status Commands

```
kubectl version

kubectl cluster-info

kubectl get pods -n kube-system

kubectl get events

```

It will take a few minutes to have the cluster up and running with all the services.


### Possible Issues
  * Nodes are node in Ready status
  * kube-dns is crashing constantly
  * Some of the systems services are not up

Most of the times, kubernetes does self heal, unless its a issue with system resources not being adequate. Upgrading resources or launching it on bigger capacity VM/servers solves it. However, if the issues persist, you could try following techniques,

Troubleshooting Tips

Check events
```
kubectl get events
```

Check Logs
```
kubectl get pods -n kube-system

[get the name of the pod which has a problem]

kubectl logs <pod> -n kube-system



## Enable Kubernetes Dashboard

After the Pod networks is installled, We can install another add-on service which is Kubernetes Dashboard.

Installing Dashboard:
```
kubectl apply -f https://gist.githubusercontent.com/initcron/32ff89394c881414ea7ef7f4d3a1d499/raw/baffda78ffdcaf8ece87a76fb2bb3fd767820a3f/kube-dashboard.yaml

```
This will create a pod for the Kubernetes Dashboard.


To access the Dashboard in th browser, run the below command
```
kubectl describe svc kubernetes-dashboard -n kube-system
```

Sample output:
```
kubectl describe svc kubernetes-dashboard -n kube-system
Name:                   kubernetes-dashboard
Namespace:              kube-system
Labels:                 app=kubernetes-dashboard
Selector:               app=kubernetes-dashboard
Type:                   NodePort
IP:                     10.98.148.82
Port:                   <unset> 80/TCP
NodePort:               <unset> 32756/TCP
Endpoints:              10.40.0.1:9090
Session Affinity:       None
```

Now check for the node port, here it is 32756, and go to the browser,


