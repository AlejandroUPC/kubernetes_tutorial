# Kubernetes namespaces explained

A Namespace is a way to organize resources.
You can have multipe Namespaces in a cluster, and you can imagine a Namespace like being a small cluster inside the entire cluster.

Kubernetes by default assigns 4 namespaces when starting the service:

```sh
kubectl get namespace
NAME              STATUS   AGE
default           Active   179m
kube-node-lease   Active   179m
kube-public       Active   179m
kube-system       Active   179m
```
Each of them:

1. Kube-system: Not for use, the components desployed in here are managing processes like Master processes, kubectl, etc...
2. Kube-public: Contains all the public accesible data, it is a ConfigMap that contains cluster information which is accessible without authetnication.
3. Kube-node-lease: Holds information about the heartbeats from the nodes.
4. default: The one that is being used to create the resources at the beginning if no Namespace is created.

New Namespaces can be created using configuration files or directly in the terminal:

```sh
kubectl create namespace my-namespace
```
## Motivation to use Namespaces

It is good to have everything in different namespaces to have more organized, and isolated, microservices.

Another case would be when having multiple teams workign in the same application to avoid possible conflicts with for example, deployments with the same name but different configuration, this would cause issues.

Also, it can be usefull to create different Namespaces to share resources between differenet environments, so they can share the resources.

Finally, it is easier to limit resources and access when using Namespaces.

## Characteristics of Namespaces

In order to start working and organizing your components into Namespacs, you must consider the following:

* You can't access most of the resoruces from another Namespace.
* Services can be shared among Namesapces.
* Some components can't be created within a Namespace, they just live globally in the entire cluster, like volumes, persisted volumes or a node.

## How to create a component in a Namespace

Before, we created components directly using files without defining a Namespace at any given point so it was assigned to a file.

We can use the applu command adding the flag namespace:

```
sh kubectl apply -f filename --namespace=my-namespace
```

Or using the file by adding the tag namespace on the metadata tag.

You can also change the default namespace by installing kubens, so you avoid adding tags all the time in the file or cli.