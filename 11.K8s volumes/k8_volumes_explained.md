# Kubernetes volumes explained

They way to persist data in Kubernetes, mainly split in three different components:

1. Peristent Volume
2. Persistent Volume Claim
3. Storage Class

We need some kind of storage that doesn't depend on the Pod lifecycle, for example or a database Pod which dies at some point, so a new copy can reuse the data.
It is improtant that the storage is available in any node in the cluster, and it should be highly available even if the entire Cluster crashed.

## Persistent Volume

A persistent volume is a cluster resource like RAM or CPU, but used to store data.It is also created by a YAML file by any other K8s component.
It needs actual physical storage like a local disk, nfs server or cloud storage.An example of a file can be found in *persistent-volume.yaml* using a NFS server.
Again, Peristent Volumes are not namespaces, which means they are accessible from all the different namespaces.

Inside the volumes we must distinguish between:

1. Local Volume Types: They are not tied to a specific node, but to a all the nodes equally and the survavility of a cluster crash scenario, so better use remote for Databases.
2. Remove Volume Types:


### Who crates the volumes?

Volumes need to be created before the Pods that use them are started.
___
**Side note**
There are two side roles in K8s:

1. K8s admin: Set up and mantains the cluster.
2. K8 users: Deploys applications.
____

It is the K8s admins that configure the storage based on the K8 users requierments, the users will also to confiure the YAML files, so the application claims the usage.This is done
by Peristent Volumes Claims.

## Persisten Volume Claims.

They are in charge to claim the volumes that are already created, an example of a file can be found in the *persistent-volume-claim.yaml* file.
But this doesn't end here, this PVC then needs to be also referenced in the Pod configuration file in order to be effective.

### ConfigMap and Secret

Those two are also types of loca volumes, but are not created by PV and PVC, they are own components from Kubernetes.
If you needed some files like a TLS certificate, which would be stored in Secret, in order to acess it you could mount it as any other Volume.


## Storage Class

If there is a big cluster where there are hundred of applications that are delpoyed daily, Admins from the Cluster would have to create the Peristent Volumes manually, and that would requiere a lot of time.

Storage class provisions Persistent Volumes dynamically when a PVC claims it. The component is defined in the *storage-class.yaml* file.
The provisione is very important, it defines which provisoner is used to create the PV out of it.
As PV they have to be claimed by a PVC, so they need to be added into the PVC file under the storageClassName key.
