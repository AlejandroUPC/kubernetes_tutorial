# Main Kubectl Commands

## CRUD commands

 * kubectl create deployment [name]: Creates a deployment
 * kubectl edit deployment [name]: Edits a deployment
 * kubectl delete deployment [name]: Deletes a deployment

 ## Status of different K8s components

 * kubectl get | nodes | pod | services | replicaset | deployment

 ## Debugging pods

 * kubectl logs [pod name]: Log to console
 * kbetctl exec -it [pod name] bin/bash: Get interactive terminal

 ## Hands on

  ___
*Small interruption*

Pod is the smallest unit but usually we are not creating them directly, there is a middle layer named Deploy, which is an abstraction over the pods.
___

 We create a pod using the command:

 ```sh
kubectl create deployment nginx-depl --image=nginx
 ```

And we get the message:

```sh
deployment.apps/nginx-depl created
```

We validate that the deployment was succesfull:

```sh
kubectl get deployment
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
nginx-depl   1/1     1            1           46s
```

Also we check that we have a pod running:

```sh
kubectl get pod
NAME                          READY   STATUS    RESTARTS   AGE
nginx-depl-5c8bf76b5b-h7lrj   1/1     Running   0          94s
```

Now there is an extra layer between Deploy and the Pods, replicaset, which takes care of controlling how many Pods are runnning for a Deploy:

```sh
kubectl get replicaset
NAME                    DESIRED   CURRENT   READY   AGE
nginx-depl-5c8bf76b5b   1         1         1       3m32s
```

As it can be observed the prefix for the replicaset and the node is the same, but the node has an extra suffix. This indicates it belogns to the replicaset, but it is an unique instance inside of it, we could have
two instances of the nginx services and each would have a different suffix.

In the previous example we could not decide which image is running in our Pod, the Deployment service picked for us. So we understand that is not the Pod's responsability to pick which image it's going to run but the Deployment, and this can be edited:

```sh
kubectl edit deployment nginx-depl
```

This will run a big YAML file where we can edit the image under the tree spec -> containers -> image. When this is done the pod runnning the old version will get terminated and a new one meeting the
requierements will be launched. And not only the Pod, but also an entire new ReplicaSet is launched.

Now to debug the Pods one of the must useful practices is to log the Pods (note a new Deployment using mongodb images was created):

```sh
kubectl logs mongodb-depl-5cf669bd6-rdcks
{"t":{"$date":"2021-05-21T23:15:56.368+00:00"},"s":"I",  "c":"CONTROL",  "id":23285,   "ctx":"main","msg":"Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'"}
{"t":{"$date":"2021-05-21T23:15:56.370+00:00"},"s":"W",  "c":"ASIO",     "id":22601,   "ctx":"main","msg":"No TransportLayer configured during NetworkInterface startup"}
{"t":{"$date":"2021-05-21T23:15:56.370+00:00"},"s":"I",  "c":"NETWORK",  "id":4648601, "ctx":"main","msg":"Implicit TCP FastOpen unavailable. If TCP FastOpen is required, set tcpFastOpenServer, tcpFastOpenClient, and tcpFastOpenQueueSize."}
```

It is also possible to get some more information about a pod using the describe command, in this case we are just showing the events, which is a part of the information displayed:

```sh
kubectl describe pod mongodb-depl-5cf669bd6-rdcks
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  2m14s  default-scheduler  Successfully assigned default/mongodb-depl-5cf669bd6-rdcks to minikube
  Normal  Pulling    2m14s  kubelet            Pulling image "mongo"
  Normal  Pulled     110s   kubelet            Successfully pulled image "mongo" in 23.07993538s
  Normal  Created    110s   kubelet            Created container mongo
  Normal  Started    110s   kubelet            Started container mongo

```

We can also attach a interactive terminal to a running Pod using:

```sh
kubectl exec -it mongodb-depl-5cf669bd6-rdcks bin/bash
```

To delete all the ReplicaSet and the Pods underneath we can use:

```sh
kubectl delete deployment mongodb-depl
```

