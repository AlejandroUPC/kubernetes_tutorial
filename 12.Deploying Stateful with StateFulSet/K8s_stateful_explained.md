# Kubernetes StatefulSet explained

## What is StatefulSet?

A component ofro stateful applications like databases or any application that stores data to keep track of its state.
On the other hand stateless applications don't keep record of the state, each request is completely new.

A NodeJs application connected to a MongoDb for example, the requests on the NodeJs would be stateless, but then the request itself might query or update the data, where MongoDb is stateful.

Stateful annd Stateless applications are deployed different ways using different components:

1. Stateless applications: Deployed using the Deploy component.
2. Stateful applications: USing StatefulSet components, like stateless you can replicate the Pods or multiple replicas.

## Deployment vs StatefulSet

Replicating the seconds are a little more difficult and has more requierments.

If we had one MysQL database that is serving requests from a Java application.
At some given point, we decide to replicate the Java applications up to three, and alaso the MySQL application. Scaling the Java application is pretty straight forward, they are identical and interchangable. The Pods will be created in any random order, with a Sevice acting as a LoadBalancer for all of them.

For the database it's a little bit more complex:

* They can't be created or deleted at the same time.
* They cant be randomly addressed.
s
That's because the replica Pods are not identical, they have their own unique Pod identity on top of the blueprint.

It maintains a sticky identity for each Pod, even if they are created from the same specification, it is persistent across any re-scheduling (if pod id-2 dies, a new one with id-2 come).
The identity is necessary is because when scaling database applications. When having a single MySQL you could have the same to read and write, but if you had two, you can't let both
to read and write, because they could lead to inconsistencies, from here comes the complexity.
The pod allowed to update the data is the Master, the other are Slaves.

It is also worth mentioning that thise Pods do not have acess to the same physical storage, they have their own repicas that each one of them can acess. Every Pod must have the exact same data at any point of the time, so they must synchronize.

The best way to keep the data safe is using StatefulSet that have Persistent Volumes mapped into each Pod, where the data is saved but also the state of himself as a Pod.

Each Pod in a StatefulSet has its own two endpoints:

1. LoadBalanced Service.
2. Individual DNS name for each Pod (deployment Pods do not have).

This means that they have predictable pod name and a fixed individual DNS name, they have a Sticky idenitity, because if a Pod dies their IP would change but not the endpoints.

## Replicating stateful applications

* It is a complex task, even if Kubernetes somehow makes it simplier.
* Lots of extra work: configure cloning and data synchronization, make remote storage available, managing and back-up. 