# Complete application setup with Kubernetes Components

How are we going to do it?

1. Creat a MongoDb Pod as an internal service (only internal requests allowed, only from same cluster).

2. Create a MongoExpress deployment, it will need a MongoDb url and credentials, we will pass it using the Deployment configuration file using environent variables. The ConfigMap will include the URI and the Secret the credentials, both inside the Deployment.yaml file.

3. We will create an external service to make MongoExpress available from outside the cluster using an IP address of the Node and a port.


## Hands on

First of all, we run a command to check all the components that are running inside a cluster:

```sh
kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   120m
```

So there is just the kubernetes cluster as a service.

## Creating the files

### Secret

The secret file *mongodb-secret.yaml* offers different ways to store credentails, we use the *Opaque* method, which is key-value for the secret data. It is important that the values for the Secrets must be base64 encrypted, **never plain text**.

It is important that in our Kuberentes cluster we  create first the Secret and then the Service, as our Service is referencing our Secret in oreder to retrieve the credentials.

We create the Secret file:

```sh
kubectl apply -f mongodb-secret.yaml 
secret/mongodb-secret created
```


### Deployment

We create the file *mongodb-deployment.yaml*, the important part in here has been to leave the authentication data because we are going to be using Secrets (they only exist in Kubernetes) to pass credentials to the containers, we leave them empty on the file.

We can now validate it has been created:

```sh
kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-vcnmr   kubernetes.io/service-account-token   3      133m
mongodb-secret        Opaque                                2      23s
```

We now deploy our Deployment:

```skubectl apply -f mongodb-deployment.yaml 
deployment.apps/mongodb-deployment created
```

And validate that everythign is running as expected:

```sh
kubectl get all
NAME                                     READY   STATUS    RESTARTS   AGE
pod/mongodb-deployment-8f6675bc5-qws2l   1/1     Running   0          33s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   140m

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongodb-deployment   1/1     1            1           33s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/mongodb-deployment-8f6675bc5   1         1         1       33s

```

### Internal Service

We now create an internal service file in the same file as the Deployment as it is usually done, we do that by creating ---, that splits a document in YAML.
This internal service will allow access to a Pod from addresses that are inside the Cluster.

We now use the same apply command as before, but kubectl detects what needs to be changed:

```sh
kubectl apply -f mongodb-deployment.yaml 
deployment.apps/mongodb-deployment unchanged
service/mongodb-service created
```

We validate that the service is created:

```sh
kubectl get services
NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE
kubernetes        ClusterIP   10.96.0.1      <none>        443/TCP     147m
mongodb-service   ClusterIP   10.96.132.89   <none>        27017/TCP   73s
```


### MongoExpress deployment

We create a normal deployment file, but also needs to know:
1. Which databasee to connect: We could use the address directly in the file, or use it in a Configmap, which is centralized and accessible for other applications.
2. Which credentials to use: Those two is on the secret.

The ConfigMap file *mongoexpress-configmap.yaml* is quite simple, as well as the way to reference them from the deploymenet file.



### Deploying MongoExpress

Again, the order of deployment matters. As the Deployment has dependencies on the ConfigMap we created, we deploy the ConfigMap first:

```sh
kubectl apply -f mongoexpress-configmap.yaml 
configmap/mongodb-configmap created
```

And then proceed to deploy:

```sh
kubectl apply -f mongoexpress-deployment.yaml 
deployment.apps/mongo-express created
```

### External service

We need to create an external service in order to access some of the Pods applications from outside the cluster.
In order to create an external service, it's almost the same as a standard internal service, but we need to change two things:

1. In the spec section we add a type of LoadBalancer.
2. on ports, add nodePort, which is the port where this external IP address will be open, the value must be between the range 30000-32767.

After deploying when we list the services we see that the application is listening to an external port, 30000, as a LoadBalancer:

```sh
mongo-express-service   LoadBalancer   10.108.99.203   <pending>     8081:30000/TCP   8s
```

Because we are using minikube, in  a real cluster this wouldn't happen, we need to assign an IP, otherwise as it would stay in the <pending> status as seen in the last terminal:

```
minikube service mongo-express-service
|-----------|-----------------------|-------------|-------------------------|
| NAMESPACE |         NAME          | TARGET PORT |           URL           |
|-----------|-----------------------|-------------|-------------------------|
| default   | mongo-express-service |        8081 | http://172.17.0.3:30000 |
|-----------|-----------------------|-------------|-------------------------|
🎉  Opening service default/mongo-express-service in default browser...
```

And this will assign an IP address finally.

