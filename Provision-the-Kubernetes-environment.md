## Provision your environment to host the Kubernetes HA cluster:

We will be using Virtual Box for the virtual machines and Vagrant to automate the provisioning.

First please install virtualbox, vagrant and git on your laptop

> VirtualBox: https://www.virtualbox.org/wiki/Downloads

> Vagrant: https://www.vagrantup.com/downloads.html

> Git: https://git-scm.com/

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

> vagrant destroy 'nodename'

Then reprovision. Only the missing VMs will be re-provisioned

> vagrant up

### Next Step: [Securing the Kubernetes Cluster](Provision-the-KPI-infrastructure.md)
