# Minikube and kubectl local setup

## Minikube

One node cluster where the master processes and node processes run in one node with a docker container pre-installed.
It runs through Virtual Box or other hypervisor and run the Nodes in there for testing purposes.

## Kubectl

A command line tool for kubernetes cluster, you can do different stuff by subimitting commands to the API server, and then the worker processes execute the commands like create and destroy pods, services, etc...
Kubectl is not just for Minikube, it works in any cluster like production, cloud,etc...

## Installation

Virtualization in your machine is needed as Minikube runs in a Virtual Box:

```sh
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

To get kubectl:

```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

To start a cluster we run:

```sh
minikube start --vm-driver=docker
```

We can use the following command to get a status of our cluster nodes:

```sh
kubectl get nodes
```
We get that at the moment we just got a unique master node, as we do not have any service running and it is the minimun unit to have a cluster:

```sh
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

It is possible to get a more overview status of the componentes running in the node using:

```sh
minikube status
```

We can see alll of the services are configured and running:

```sh
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

To sum it up, kubectl is for configuring and interacting with the Minikube cluster, and Minikube CLI is just to start and delete the cluster.