# Kubernetes architecture

Based on master-node nodes.


## Node processes

Every Node (workers) has multiple Pods on it, every Node has 3 processes that take care of schedule and management tasks:

1. Container runtime: Like for example Docker for the containers running within
2. Kubelet: schedules those pods and containers as it interacts with the container and the node and is responsible to take the configuration and run a pod with the container inside, and also manage the resources.
3. Kubeproxy: Responsable to forwarnding requests to Pods, it has it's own logic inside to ensure that the communications works in a performance way with low overhead. For example if we have two nodes, each node has one web app and a database, the proxy will always try to send the petitions from the webapp to the database that are in the same node, if possible.

## Master processes

They have completely different services running as the Node processes, mostly for orchestration, management, etc. There is usually more than one Master.
Usually need less resources but they are critical.
Masters  have 3 processes:

1. Api server: Server where the user can interact to handle services, it can be seen as a cluster gateway that get's the initial requests or updates. It also acts as a gatekeeper as authentication.
2. Scheduler: To schedule a new pod, for example. It has an internal logic to decide which Node will the Pod be placed at based on the resources requested by the Pod and the available resources on every Node. **Important** the Scheduler only decides where the Pod will be placed, but it is **Kubelet** that takes care of the deploying.
3. Controller Manager: Detects the cluster state change like crashing of Pods for exmaple, trying to recover state as soon as possible.
4. etcd: It's a Key Value store of a cluster state, could be imagined like the brain. All of the changes that happen to the cluster are stored in here. All the listed processes know the state of the cluster based on the data stored in here like cluster health, available resources, state changes,etc... **Important** The actual application data is not stored in the etcd.