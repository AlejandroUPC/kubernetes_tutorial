# Kuberntes Ingress explained

## Exteranl Service vs Ingress

Imagine a Kubernetes cluster where we have a Pod, my-application, and a external service, my-app Service.
The my-app Service should be accessible from outside the cluster to acecss the my-app Pod. With the external Service it would look something like http://ip:port, when
ideally we would want something like https://my-app.com, so using https for security and a domain name.

To get the last, we create an internal Service and then we create an Ingress service, so our service is not exposed directly outside the Cluster, but it goes throught Ingress.

In the file *ingress-example.yaml* you can see an example and how it differs from an external Service, and also how the internal Service is setup in the file *ingress-internalservice.yaml*.


## How to configure Ingress in the Cluster

Creating the Ingress just from the configuration file is not enough, we need to create an implementation which is called Ingress Controller.
This needs to be installed, which is basically a set or unique Pod that does evaluating and processing from the Ingress rules, there is one from Kubernetes that used Nginx.

## Set up the cluster for external requests.

We are using Minikube to make a small architecture example.

1. We installl Ingress controller, that starts the Nginx controller.

```sh
minikube addons enable ingress
```

We validate it is running:

```sh
kubectl get pod -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-5tvq9        0/1     Completed   0          9m28s
ingress-nginx-admission-patch-bs2j7         0/1     Completed   2          9m28s
ingress-nginx-controller-5d88495688-tljd4   1/1     Running     0          9m28s
```

We create a rule from the internal kubernes dashboard that is actually running using an ip-port:

```sh
kubectl get all -n kubernetes-dashboard
NAME                                            READY   STATUS    RESTARTS   AGE
pod/dashboard-metrics-scraper-f6647bd8c-xxc84   1/1     Running   0          55s
pod/kubernetes-dashboard-968bcb79-26djc         1/1     Running   0          55s

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/dashboard-metrics-scraper   ClusterIP   10.100.30.237    <none>        8000/TCP   55s
service/kubernetes-dashboard        ClusterIP   10.101.102.208   <none>        80/TCP     55s

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dashboard-metrics-scraper   1/1     1            1           55s
deployment.apps/kubernetes-dashboard        1/1     1            1           55s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/dashboard-metrics-scraper-f6647bd8c   1         1         1       55s
replicaset.apps/kubernetes-dashboard-968bcb79         1         1         1       55s
```

To do so we create the *ingress-rule.yaml* file to forward the request recieved to dashboard.com to the internal setvice.

We create the rule:

```sh
kubectl apply -f ingress-rule.yaml 
Warning: networking.k8s.io/v1beta1 Ingress is deprecated in v1.19+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.networking.k8s.io/dashboard-ingress created
```

We check that it has been created succesfully:

```sh
kubectl get ingress -n kubernetes-dashboard
NAME                CLASS    HOSTS           ADDRESS      PORTS   AGE
dashboard-ingress   <none>   dashboard.com   172.17.0.3   80      45s
```

But now in order to resolve locally the domain dashboard.com to our internal IP we need to modify /etc/host file adding the line at the end.

```sh
172.17.0.3 dashboard.com
```


## Ingress default backend

If we run the command:

```shkubectl describe ingress dashboard-ingress -n kubernetes-dashboard
Name:             dashboard-ingress
Namespace:        kubernetes-dashboard
Address:          172.17.0.3
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host           Path  Backends
  ----           ----  --------
  dashboard.com  
                    kubernetes-dashboard:80 (172.18.0.6:9090)
Annotations:     <none>
Events:
  Type    Reason  Age                    From                      Message
  ----    ------  ----                   ----                      -------
  Normal  Sync    3m14s (x2 over 3m42s)  nginx-ingress-controller  Scheduled for sync

```

We can see there is a default Backend to handle unmapped request (in this case we do not have the service running), so we create the *default-http-ingress.yaml* file.

## Ingress configurations

With Ingress you can do much more than simply forwarding rules, which are seen in the next section:

1. Defining multiple paths for the same host.
2. Having multiple sub-domains or domains.
3. Configuring TLS certificate (https).