# YAML Configuration File in Kubernetes

The way to connect Deployment and Pods, it is split in three parts:

1. Metadata: Like name, labels...


2. Specification(spec). Specification of the apiVersion and the kind (service, deployment,etc...). Note that the attribtues (inside the spec) are particular to every kind.


3. Status: It is automatically generated and edited by Kubernetes, by checking the desired state against the actual state. If those do not match Kubernetes knows a change is needed, which is the basis of the self-healing service that Kubernetes provides. The status data comes from the previously mentioned **etcd**, that holds the current status of any Kubernetes component.

Usually the configuration files are stored together with your application code.

## Connecting components


This is done using labels and selectors, metadata contains the labels, and specifications contains the selector even if they are in different files (e.g. Deployment - Service)

In order to create more complex deployments, instead to use the CLI it is recommended to use YAML files to configure and specify Kubernetes to read from there:


```sh
kubectl apply -f nginx-deployment.yaml
```

The file can be found in this very same folder.

Note that with kubectl you can create, but also update an existing component.

We now create a Deployment and a Service using the files from the previous chapter:

```sh
kubectl apply -f nginx-deployment.yaml
kubectl apply -f ngnix-service.yaml
```

And proceed to validate both:

```sh
kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-f4b7bbcbc-nxhj8   1/1     Running   0          54s
nginx-deployment-f4b7bbcbc-t2jz9   1/1     Running   0          54s

```


```sh
kubectl get services
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP   98m
nginx-service   ClusterIP   10.101.153.112   <none>        80/TCP    44s
```

We can get further information of the serivce using:

```sh
kubectl describe service nginx-service
Name:              nginx-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP Families:       <none>
IP:                10.101.153.112
IPs:               10.101.153.112
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         172.18.0.3:8080,172.18.0.4:8080
Session Affinity:  None
Events:            <none>
```

Another usefull command to get extra information from the Pods is:

```sh
kubectl get pod -o wide
```

It is also possible to get the Status (the part that is automatically generated from Kubernets):

```sh
kubectl get deployment nginx-deployment -o yaml
```

Which among other returns:

```sh
status:
  availableReplicas: 2
  conditions:
  - lastTransitionTime: "2021-05-21T23:50:06Z"
    lastUpdateTime: "2021-05-21T23:50:06Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2021-05-21T23:50:04Z"
    lastUpdateTime: "2021-05-21T23:50:06Z"
    message: ReplicaSet "nginx-deployment-f4b7bbcbc" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 2
  replicas: 2
  updatedReplicas: 2
```

We can finally delete:


```sh
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```