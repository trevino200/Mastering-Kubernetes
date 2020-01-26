
## Just Enough Containers to be dangerous:

The rise of cloud native applications and the shift from monolithics apps to microservices. A new type of infrastructure to run those apps became apparent which needed Kubernetes and containers. 
Monolithic apps are composed of various components that are tightly coupled together and running under the same OS process on a server whereas cloud native apps are composed of many components running on independent processes called microservices which communicate with each other using well defined APIs.Each of these components can be changed or scaled independently from each other. This allows microservices apps to scale out horizontaly.
Providing a consistent environment for developers in order not to deal with OS changes, missing libraries etc.. and be able to continuously deploy their apps regardless of the udnerlying environment. Such requirement gave the rise for containers.

The first step is install Docker and in the case docker is installed on a VM running Ubuntu. You can install Docker on Mac or Windows as well as any linux OS.You can find the installation guide on docker.com site.
In this lab, the installation is done while provsioning the VM using Vagrant. The install-docker.sh script installs docker on the Ubuntu VM.

All containers images are stored on the docker Hub repo but can installed locally as well. 
The first step is to provision your Hello World container using nginx using the docker run command. Docker supports multiple versions of a container image named tags. Omitting the tage name means that it is the latest variannt.
```
docker <run>:<tag>
```


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
we can test the app by installing node and npm and verify the version after the installation or even better by packaging the app in a docker container image. Packaging an app in a container image is done by creating a Dockerfile which contains the needed instructions for Docker to build the container image. The Dockerfile has to be in the same directory as the node.js app.
I would recommed creating a directory to store the Dockerfile and node.js application

```
dean:dockerimages dasig$ pwd
/Users/dasig/dockerimages
dean:dockerimages dasig$ ls
Dockerfile	web-server.js
dean:dockerimages dasig$ docker build -t testwebserver .
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM node
latest: Pulling from library/node
146bd6a88618: Pull complete 
9935d0c62ace: Pull complete 
db0efb86e806: Pull complete 
e705a4c4fd31: Pull complete 
c877b722db6f: Pull complete 
645c20ec8214: Pull complete 
085ea863d8e5: Pull complete 
1af68df2ebb5: Pull complete 
e29006b03380: Pull complete 
Digest: sha256:78eaa46faf76c5ffaf88f15314909703ac9cc71fee3314b92d087320c0bd0a30
Status: Downloaded newer image for node:latest
 ---> 2a0d8959c8e1
Step 2/4 : MAINTAINER DeanH
 ---> Running in e3e010ded8a3
Removing intermediate container e3e010ded8a3
 ---> 88166890a7dd
Step 3/4 : ADD web-server.js /web-server.js
 ---> 5e749967e7e2
Step 4/4 : ENTRYPOINT ["node", "web-server.js"]
 ---> Running in 8bcfbeb50ddb
Removing intermediate container 8bcfbeb50ddb
 ---> 23802c78f6a2
Successfully built 23802c78f6a2
Successfully tagged testwebserver:latest
dean:dockerimages dasig$ 
dean:dockerimages dasig$ 
dean:dockerimages dasig$ docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
testwebserver               latest              23802c78f6a2        6 minutes ago       939MB
node                        latest              2a0d8959c8e1        3 days ago          939MB

'''



















### Next Step: [Provision the lab environment for Kubernetes HA Cluster](Provision-the-Kubernetes-environment.md)
