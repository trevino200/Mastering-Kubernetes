# Mastering Kubernetes " The Hard Way"

Mastering Kubernetes by installing a Kubernetes cluster the hard way. This is based on Kelsey Hightower book on installing Kubernetes the hard way.
Kelsey showed how to install the cluster on GCP and in this lab, I will guide you on how to install on your laptop using virtual box running Ubuntu.
The kubernetes cluster will use 5 VMs in total in this lab. You will have HA between 2 master nodes, 2 worker nodes and 1 load balancer to front the master nodes. 
Vagrant will automate the provisioning of the 5 VMs which will be used to create the Kubernetes cluster. The cluster will be composed of 2 masters in a HA configuration, 2 worker nodes and 1 load balancer fronting the master nodes. The load balancer distributed the API requests from the worker nodes to each master node API server.

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

> vagrant up

Note:
- a DNS entry has been added to the /etc/hosts file has been added for internet connectivity
    > DNS: 8.8.8.8
- Docker will be installed on each worker nodes or minions.


### 1. SSH to the nodes

  In the Vagrant dir, run the following commands,
  
  > vagrant ssh <nodename> 
  
  > sudo su
  
- Note: You can ssh directly to each node but the Username and password based SSH is disabled by default. you would have to use the    private key which is created by Vagrant and is located at the following location: 
the virtual machine name is the virtual box VM name and NOT the ubuntu hostname

**Private Key Path:** `.vagrant/machines/< virtual machine name>/virtualbox/private_key`

**Username:** `vagrant`


## Verify Environment

- Ensure all VMs are up
- Ensure VMs are assigned the above IP addresses
- Ensure you can SSH into these VMs 
- Ensure the VMs can ping each other
- Ensure the worker nodes or minions have Docker installed on them. Version: 18.06
  > command `sudo docker version`

## Maintenance

If you would like to start over or if any of the VMs failed to provision you can delete the VM with the following command:

> vagrant destroy <vm>

Then reprovision. Only the missing VMs will be re-provisioned

> vagrant up

