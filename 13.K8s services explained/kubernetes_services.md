# Kubernetes services overview

## What is a Service and why do we need it?

Each Pod gets its own internal IP address, but Pods are ephemeral, they are replaced frequently, and they get a new IP when does this happens.
This means it is not smart to use the Pods IP addresses to create connection betwwen them as they can change all the time. Services allows us to gete a static IP even when they die 
if we set a Service in front of the Pod.
Services can also act as a LoadBalancer when they are in front of a replica set, so only calling a single IP we can reach many Pods.

## ClusterIP Services

The most common one, it is the default type. Imagine we had an application container and a log container running on a Pod, the last one just takes the logs of the first and process them somehow. The container are running on port 3000 and 9000 specifically, they are both accessible from the Pod that also gets it's own IP address.

Now let's assume we set a replica facto of 2, so we would have the same setup but with a different Pod's IP on another Node. How does Ingress handles the calls on the different Nodes? It does through a ClsterIP Service.

## Headless Service

When a client want's t to communicate to a specific Pod directly without going to the Service, or a Pod to Pod directly, ignoring the ClusterIP Service.
A use case would be stateful applications, like databases, the Pods replicas are not identical.
To do so we would need to get the IP address for every individual Pod, and that's not very efficient as IPs change. A better approach is to use DNS lookup, so the DNS server returns
to a single IP Adress theat belongs to a single service (but it will be the ClusterIP, not the Pod).
To solve so we need to set the ClusterIP to None in the configuration, and the DNS lookup will return the Pod address. 
This way, by setting ClusterIP to None we define a Headless Service.

## Service Type attributes

When creating a Service we can set the type to:

1. ClusterIP: Default, type not needed and acts as internal Service.
2. NodePort: Creates a service that is accessible on a static port on each worker node in the cluster, external traffic is possible in the port set to nodePort in the configuration which makes it not very secure.
3. LoadBalancer: The service becomes externally through a cloud provider load balancer functionality (ELB for AWS, for example).