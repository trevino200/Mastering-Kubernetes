
## Just Enough Containers to be dangerous:

The rise of cloud native applications and the shift from monolithics apps to microservices. A new type of infrastructure to run those apps became apparent which needed Kubernetes and containers. 
Monolithic apps are composed of various components that are tightly coupled together and running under the same OS process on a server whereas cloud native apps are composed of many components running on independent processes called microservices which communicate with each other using well defined APIs.Each of these components can be changed or scaled independently from each other. This allows microservices apps to scale out horizontaly.
Providing a consistent environment for developers in order not to deal with OS changes, missing libraries etc.. and be able to continuously deploy their apps regardless of the udnerlying environment. Such requirement gave the rise for containers.

The first step is install Docker and in the case docker is installed on a VM running Ubuntu. You can install Docker on Mac or Windows as well as any linux OS.You can find the installation guide on docker.com site.
In this lab, the installation is done while provsioning the VM using Vagrant. The install-docker.sh script installs docker on the Ubuntu VM.

All containers images are stored on the docker Hub repo but can installed locally as well. 
The first step is to provision your Hello World container using busybox OS.

```
root@minion-3:~# docker run nginx echo "hello world"
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
8ec398bc0356: Pull complete
a53c868fbde7: Pull complete
79daf9dd140d: Pull complete
Digest: sha256:70821e443be75ea38bdf52a974fd2271babd5875b2b1964f05025981c75a6717
Status: Downloaded newer image for nginx:latest
hello world
root@minion-3:~#

```
you can verify that the or all container is running, you can use the docker ps -a command allow to list all containers.

```
root@minion-3:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                         PORTS               NAMES
ec89cd2cd8c5        nginx               "echo 'hello world'"   2 minutes ago       Exited (0) 2 minutes ago                           reverent_mendeleev
960c1d33d449        alpine              "echo 'Hello World'"   5 minutes ago       Exited (0) 5 minutes ago                           determined_ardinghelli
5b86f430b87a        busybox             "echo 'Hello World'"   6 minutes ago       Exited (0) 6 minutes ago                           quirky_knuth
4d5859f0c29e        busybox             "Hello Worl"           6 minutes ago       Created                                            happy_swartz```  xenodochial_davinci
5fbaaa6f310c        alpine              "echo 'Hello World'"   31 minutes ago      Exited (0) 31 minutes ago                          zealous_jackson
642cf5af8b06        busybox             "echo 'Hello World'"   31 minutes ago      Exited (0) 31 minutes ago                          stoic_fermi
45427c2dd281        busybox             "echo 'Hello World'"   About an hour ago   Exited (0) About an hour ago                       nostalgic_goldstine
root@minion-3:~#

```
containers are assigned a random NAME and ID ie reverent_mendeleev and ec89cd2cd8c5. This ID and NAME are important as they will be needed to perform administration on the container. The NAME can be reconfigured to a simpler name.

A container can be deleted using the docker rm command using the name or ID. Relisting the containers confirm that the container has been deleted

```
root@minion-3:~# docker rm  reverent_mendeleev
reverent_mendeleev
root@minion-3:~#
root@minion-3:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                         PORTS               NAMES
960c1d33d449        alpine              "echo 'Hello World'"   9 minutes ago       Exited (0) 9 minutes ago                           determined_ardinghelli
5b86f430b87a        busybox             "echo 'Hello World'"   10 minutes ago      Exited (0) 10 minutes ago                          quirky_knuth
4d5859f0c29e        busybox             "Hello Worl"           10 minutes ago      Created                                            happy_swartz
b77066462f30        busybox             "Hello World"          10 minutes ago      Created                                            xenodochial_davinci
5fbaaa6f310c        alpine              "echo 'Hello World'"   35 minutes ago      Exited (0) 35 minutes ago                          zealous_jackson
642cf5af8b06        busybox             "echo 'Hello World'"   36 minutes ago      Exited (0) 36 minutes ago                          stoic_fermi
45427c2dd281        busybox             "echo 'Hello World'"   About an hour ago   Exited (0) About an hour ago                       nostalgic_goldstine
root@minion-3:~#

```
we can list all the container images available locally
```
root@minion-3:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              5ad3bd0e67a9        4 days ago          127MB
alpine              3.11.3              e7d92cdc71fe        8 days ago          5.59MB
alpine              latest              e7d92cdc71fe        8 days ago          5.59MB
busybox             latest              6d5fcfe5ff17        4 weeks ago         1.22MB
root@minion-3:~#
```

We can delate any container images with command below
```
root@minion-3:~# docker rmi busybox
Error response from daemon: conflict: unable to remove repository reference "busybox" (must force) - container 45427c2dd281 is using its referenced image 6d5fcfe5ff17
root@minion-3:~#
root@minion-3:~# docker rmi -f  busybox
Untagged: busybox:latest
Untagged: busybox@sha256:6915be4043561d64e0ab0f8f098dc2ac48e077fe23f488ac24b665166898115a
Deleted: sha256:6d5fcfe5ff170471fcc3c8b47631d6d71202a1fd44cf3c147e50c8de21cf0648
root@minion-3:~#
root@minion-3:~#
root@minion-3:~# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              5ad3bd0e67a9        4 days ago          127MB
alpine              3.11.3              e7d92cdc71fe        8 days ago          5.59MB
root@minion-3:~#

```
We can only download the container image without running the container using the docker pull command
```
root@minion-3:~# docker pull busybox
Using default tag: latest
latest: Pulling from library/busybox
bdbbaa22dec6: Already exists
Digest: sha256:6915be4043561d64e0ab0f8f098dc2ac48e077fe23f488ac24b665166898115a
Status: Downloaded newer image for busybox:latest
root@minion-3:~#
root@minion-3:~#

```
### Lets write our own app and wrap it in a container

I will write a basic node.js app for a Hello World web server

```
const http = require('http');

const hostname = '127.0.0.1';
const port = 8181;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});

```


















### Next Step: [Provision the lab environment for Kubernetes HA Cluster](Provision-the-Kubernetes-environment.md)
