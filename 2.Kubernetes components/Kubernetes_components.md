# Kubernetes main components

## Node

Known as a worker node, a server (physical or virtual), can contain multiple Pods.


## Pod

Smallest unit of K8s, a Pod is an abstraction over a container, it creates the running layer in the top of the container, K8s wants to extract away the container technologies so they can be replaced and to not interact
with Docker (for example) or any other container technology, **you only interact with the kubernetes layer**.

You should only run one application per Pod.

K8s offer it's own private network, where each Pod has it's own IP to communicate, however Pods components are ephemeral (it should be expected they could die), and when created **they will get a new address**, the component services solves this issue.

## Services

It is a static IP address that can be attached to each Pod, so every application will have it's own service. The life cycle of a Pod and a Service are not attached, if the Pod dies the service will still maintain the IP.
A external service is a service that opens the communication from external sources, for example a web application. External services are mapped as ip:port, where the IP is *"an ugly"* combination of numbers and a port, ideally you
want a website-like name, this is solved by Ingress.
For services that shuld not be accesisble they should be private services, such as databases.

## Ingress

Ingress could be seen like an extra layer, or a router, where it will take the request of a given service like http://my-app.com and route it to the corresponding service.

## ConfigMap

Pods communicate with eachother using a service, the endpoints of the application or any other parameters are configured using ConfigMap.
It basically is an external configuration to your application, that is then linked to the Pod and can be accessed using environment variables or a properties file.
**Don't put credentials on plain text in ConfigMap**, this is done by secret.


## Secret

A ConfigMap used to store secret data like credentials. encoded in base64 encoded format.

## Volumes

Handles the data storage of the Pods, to ensure data persistency applications that need it such as databases.
It attaches a physical storage on a hard drive from your local machine or remote such as cloud, to your Pod. Keep in mind that K8s **does not manage any data peristence**, that has to be done by the administrator.

## Deployment

We can replicate all of our Pods in different nodes, so for example if we need to deploy a new image, we would experience no downtime as we could still access the Node 2 during the deployment while Node 1 updates and vice versa.
This is done using Services, they are not just a way to have a permanent IP but also act as load balancers.

Using Deployment, you can configure the multiple deployments of a Pod, you can set a lot of parameters to configure and it can be understood as an abstraction layer for Pods.

In practice you usually works with Deployments and not Pods.


## Stateful Set

A replication solution for Pods that need to have are stateful such as databases. Stateful Set takes care of which pods are reading or writing from a storage to avoid data inconsistences.
Deploying databases applications using Stateful Set can be tedious, usually DBs are often hosted outside of a K8s cluster.