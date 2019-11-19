Mastering Kubernetes " The Hard Way"

Mastering Kubernetes by installing a Kubernetes cluster the hard way. This is based on Kelsey Hightower book on installing Kubernetes the hard way.
Kelsey showed how to install the cluster on GCP and in this lab, I will guide you on how to install on your laptop using virtual box running Ubuntu.

# Provisioning Compute Resources

Note: You must have VirtualBox and Vagrant configured at this point

Download this github repository and cd into the vagrant folder

> git clone https://github.com/dean-houari/Mastering-Kubernetes.git

CD into vagrant directory

> cd Mastering-Kubernetes/LAB/Vagrant

Run Vagrant up

> vagrant up

Vagrant will provision 5 VMs which will be used to create the Kubernetes cluster. The cluster will be composed of 2 masters in a HA configuration, 2 worker nodes and 1 load balancer fronting the master nodes. The load balancer distributed the API requests from the worker nodes to each master node API server.

The topology details are as follow: 
Note: you can change hostnames and networking paramaters in the Vagrantfile.

- The IP address range is 192.168.50.0/24

    | hostname     |  VM Name      | Role          | IP            | Forwarded Port   |
    | ------------ | --------------|:-------------:| -------------:| ----------------:|
    | master-1     | Master-1      | Master        | 192.168.50.101 |     2711         |
    | master-2     | Master-2      | Master        | 192.168.50.102 |     2712         |
    | workernode-1 | Minion-1      | Worker Node   | 192.168.50.201 |     2730         |
    | workernode-2 | Minion-2      | Worker Node   | 192.168.50.202 |     2721         |
    | lb           | LoadBalancer  | Load Balancer | 192.168.50.30  |     2722         |
    

- a DNS entry has been added to the /etc/hosts file has been added for internet connectivity
    > DNS: 8.8.8.8
- Docker will be installed on each worker nodes or minions.


## SSH to the nodes

There are two ways to SSH into the nodes:

### 1. SSH using Vagrant

  From the directory you ran the `vagrant up` command, run `vagrant ssh <vm>` for example `vagrant ssh master-1`.
  > Note: Use VM field from the above table and not the vm name itself.

### 2. SSH Using SSH Client Tools

Use your favourite SSH Terminal tool (putty).

Use the above IP addresses. Username and password based SSH is disabled by default.
Vagrant generates a private key for each of these VMs. It is placed under the .vagrant folder (in the directory you ran the `vagrant up` command from) at the below path for each VM:

**Private Key Path:** `.vagrant/machines/<machine name>/virtualbox/private_key`

**Username:** `vagrant`


## Verify Environment

- Ensure all VMs are up
- Ensure VMs are assigned the above IP addresses
- Ensure you can SSH into these VMs using the IP and private keys
- Ensure the VMs can ping each other
- Ensure the worker nodes have Docker installed on them. Version: 18.06
  > command `sudo docker version`

## Troubleshooting Tips

If any of the VMs failed to provision, or is not configured correct, delete the vm using the command:

> vagrant destroy <vm>

Then reprovision. Only the missing VMs will be re-provisioned

> vagrant up

