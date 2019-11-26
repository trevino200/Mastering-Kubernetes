
## Just Enough Containers to be dangerous:

The rise of cloud native applications and the shift from monolithics apps to microservices. A new type of infrastructure to run those apps became apparent which needed Kubernetes and containers. 
Monolithic apps are composed of various components that are tightly coupled together and running under the same OS process on a server whereas cloud native apps are composed of many components running on independent processes called microservices which communicate with each other using well defined APIs.Each of these components can be changed or scaled independently from each other. This allows microservices apps to scale out horizontaly.
Providing a consistent environment for developers in order not to deal with OS changes, missing libraries etc.. and be able to continuously deploy their apps regardless of the udnerlying environment. Such requirement gave the rise for containers.










### Next Step: [Provision the lab environment for Kubernetes HA Cluster](Provision-the-Kubernetes-environment.md)
