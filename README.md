# Mastering Kubernetes..."The Hard Way"

Mastering Kubernetes by installing a Kubernetes cluster the hard way. This is based on Kelsey Hightower book on installing Kubernetes the hard way.
Kelsey showed how to install the cluster on GCP and in this lab, I will guide you on how to install on your laptop using virtual box running Ubuntu.
The kubernetes cluster will use 5 VMs in total in this lab. You will have HA between 2 master nodes, 2 worker nodes and 1 load balancer to front the master nodes. 
Vagrant will automate the provisioning of the 5 VMs which will be used to create the Kubernetes cluster. The cluster will be composed of 2 masters in a HA configuration, 2 worker nodes and 1 load balancer fronting the master nodes. The load balancer distributed the API requests from the worker nodes to each master node API server.

>This will allow to run a full Kubernetes cluster that can be used add security, devops ci/cd etc..

The topology details are as follow: 
Note: you can change hostnames and networking paramaters in the Vagrantfile.
  
  ![header image](https://github.com/dean-houari/Mastering-Kubernetes/blob/master/LAB/K8stopo.png)
       

## Provision your environment to host the Kubernetes HA cluster:

We will be using Virtual Box for the virtual machines and Vagrant to automate the provisioning.

First please install virtualbox, vagrant and git on your laptop

VirtualBox: https://www.virtualbox.org/wiki/Downloads

Vagrant: https://www.vagrantup.com/downloads.html

Git: https://git-scm.com/

Download this github repository using git:

> git clone https://github.com/dean-houari/Mastering-Kubernetes.git

This will copy all the lab repo to your laptop in a location of your choice. It will do that in your home dir by default.

For windows user, get putty or Conem as terminal to manage your cluster: https://www.puttygen.com/download-putty https://www.fosshub.com/ConEmu.html

go into the Vagrant directory

> cd Mastering-Kubernetes/LAB/Vagrant

### Bring up your environment

from your laptop terminal, run the following command which will bring up all the kubernetes cluster nodes:
All vagrant command should be run under the Vagrant dir.

> vagrant up

### 1. SSH to each node to verify your enviromnent:
  
  > vagrant ssh <nodename> 
  
  The nodename is the Kubernetes node name and not the VBOX VM name.
  
  > sudo su
 
 If you would like to ssh directly to the VM, you can use the vagrant private key but would recommend to just the vagrant ssh and open a terminal for each of the master and worker nodes. You can also ssh from one master node to all the other nodes using the ssh command providing you have copied the public ssh key to all nodes in the .ssh/authorized_keys file
dockers has been installed only on the worker nodes as that is where containers are provisioned. You can verify that docker has been installed successfuly by running the command:

> docker version 

## Resetting your environment 

If you would like to start over or if any of the VMs failed to provision succesfully, you can delete the VM with the following command:

> vagrant destroy 

> vagrant destroy <nodename> 

Then reprovision. Only the missing VMs will be re-provisioned

> vagrant up

### Next Step: [Provision the KPI infrastructure to secure communication to the API Server](Provision-the-KPI-infrastructure.md)
