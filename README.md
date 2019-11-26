# Mastering Kubernetes for Cloud Native Security
The 4Cs of Cloud Native Security as defined by the CNCF require a good understanding of Kubernetes and Containers. The 4Cs encompass code,container,kubernetes(COE) and Cloud(hybrid, multi, private,on-prem) and are depending on each other. Securing each element of the 4Cs is critical.
https://kubernetes.io/docs/concepts/security/overview/
           
   ![header image](https://github.com/dean-houari/Mastering-Kubernetes/blob/master/LAB/4c.png)
             
this lab Mastering Kubernetes will enable you to deploy a Kubernetes High Availabity cluster from the ground up. This is based on Kelsey Hightower book on installing Kubernetes the hard way and it is the best approach to truly understand and learn to troubleshoot Kubernetes.
Kelsey showed how to install a HA cluster on GCP and in this lab, I will guide you on how to install on your laptop using virtual box running Ubuntu. 
A Kubernetes cluster is composed of controllers called master nodes running all the control plane services that will be the COE or container orchestration enginer and the worker nodes where the containers will be creating in pods. Each pod in the worker nodes or apps will house one or more containers though the best practice is to have one container per pod.
The kubernetes cluster will use 5 VMs with 2 master nodes, 2 worker nodes and 1 load balancer to front the master nodes. 
Vagrant will automate the provisioning of the needed VMs which will be used to create the Kubernetes cluster. The cluster will be composed of 2 masters in a HA configuration, 2 worker nodes and 1 load balancer fronting the master nodes. The load balancer distributed the API requests from the worker nodes to each master node API server.

>This will allow to run a full Kubernetes cluster that can be used add security, devops ci/cd etc..

The topology details are as follow: 
Note: you can change hostnames and networking paramaters in the Vagrantfile.
  
  ![header image](https://github.com/dean-houari/Mastering-Kubernetes/blob/master/LAB/K8stopo.png)
  
  
## Understanding the Kubernetes Architecture:

With the rise of cloud netive applications and microservices where each app component are running as independent processes and exposed with a well know API, the need to find a new way to manage all these components became apparent. As these components grow in number as they scale out, it becomes very hard to manage them all while reducing the OPEX at the same time. Google devised a distributed management tool to deploy and manage these microservices components while managing to keep the operational costs low.
Kubernetes allows the developper to run apps on hundreds or thousands of nodes as it were one large logical node.Kubernetes completely abstracts the underlying infrastructure and the developer does not have to understand or care about the details of the underlying infrastructure. Kubernetes uses a fully declarative policies to deploy, manage and scale the apps.
Kubernetes is designed to deploy and manage apps in containers.

### The Kubernetes cluster is composed of two types of nodes:

 > The Master Nodes which host the Kubernetes control planes and manage the whole cluster.
  
 > The Worker Nodes which hosts the applications that have been deployed in the cluster.
  
#### The Kubernetes Control plane is composed of 4 componenets that are managing the cluster:

  > The Controller Manager or kube-controller
  
  > The Scheduler or kube-scheduler
  
  > The API server or kube-apiserver
  
  > etcd or etcd database
  
#### The Kubernetes Worker Nodes hosts the containerized apps are composed of 3 components:

  > Kubelet 
   
  > Kube-Proxy
   
  > Container runtime (Docker, cotainerd, rkt) ...(Docker being the most common one used)
   
### https://kubernetes.io/docs/concepts/overview/components/




### Next Step: [Just enough Containers to be dangerous](Just-Enough-Containers.md)
