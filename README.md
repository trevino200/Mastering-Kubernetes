# Mastering Kubernetes..."The Hard Way"

Mastering Kubernetes by installing a Kubernetes cluster the hard way. This is based on Kelsey Hightower book on installing Kubernetes the hard way.
Kelsey showed how to install the cluster on GCP and in this lab, I will guide you on how to install on your laptop using virtual box running Ubuntu.
The kubernetes cluster will use 5 VMs in total in this lab. You will have HA between 2 master nodes, 2 worker nodes and 1 load balancer to front the master nodes. 
Vagrant will automate the provisioning of the 5 VMs which will be used to create the Kubernetes cluster. The cluster will be composed of 2 masters in a HA configuration, 2 worker nodes and 1 load balancer fronting the master nodes. The load balancer distributed the API requests from the worker nodes to each master node API server.

>This will allow to run a full Kubernetes cluster that can be used add security, devops ci/cd etc..

The topology details are as follow: 
Note: you can change hostnames and networking paramaters in the Vagrantfile.
  
  ![header image](https://github.com/dean-houari/Mastering-Kubernetes/blob/master/LAB/K8stopo.png)
       




### Next Step: [Provision the lab environment for Kubernetes HA Cluster](Provision-the-Kubernetes-environment.md)
