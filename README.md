# Mastering Kubernetes..."The Hard Way"

this lab Mastering Kubernetes will enable you to deploy a Kubernetes High Availabity cluster from the ground up. This is based on Kelsey Hightower book on installing Kubernetes the hard way and it is the best approach to truly understand and learn to troubleshoot Kubernetes.
Kelsey showed how to install a HA cluster on GCP and in this lab, I will guide you on how to install on your laptop using virtual box running Ubuntu. 
A Kubernetes cluster is composed of controllers called master nodes running all the control plane services that will be the COE or container orchestration enginer and the worker nodes where the containers will be creating in pods. Each pod in the worker nodes or apps will house one or more containers though the best practice is to have one container per pod.
The kubernetes cluster will use 5 VMs with 2 master nodes, 2 worker nodes and 1 load balancer to front the master nodes. 
Vagrant will automate the provisioning of the needed VMs which will be used to create the Kubernetes cluster. The cluster will be composed of 2 masters in a HA configuration, 2 worker nodes and 1 load balancer fronting the master nodes. The load balancer distributed the API requests from the worker nodes to each master node API server.

>This will allow to run a full Kubernetes cluster that can be used add security, devops ci/cd etc..

The topology details are as follow: 
Note: you can change hostnames and networking paramaters in the Vagrantfile.
  
  ![header image](https://github.com/dean-houari/Mastering-Kubernetes/blob/master/LAB/K8stopo.png)
       




### Next Step: [Provision the lab environment for Kubernetes HA Cluster](Provision-the-Kubernetes-environment.md)
