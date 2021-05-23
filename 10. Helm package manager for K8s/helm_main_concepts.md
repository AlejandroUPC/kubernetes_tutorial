# Helm main concepts

## What is Helm?

Helm is a package manager for Kuberenetes, such as yum, apt,etc... to package YAML files and distribute them in public and private repositories.
For example, having an application in your kuberentes server and you would like to deploy ElasticSearch to collect the logs. Deploying ElasticSearch we would need a bunch fo components: StatefulSet, ConfigMap, Secret, K8s user with permissions and more services... which would take a lot of time.

Here is where Helm comes in handy, a Helm Chart is a bunch of YAML files (bundles) that enable to have a service running faster, the commons are: mongodb, prometheus, mysql,etc...

We can access them, using:

```sh
helm search [name]
```

Another functionallity is that Helm is a templating engine, so if we had an application that has multiple microservices, which are almost the same file but small differences like version tags.
Without helm we would have to write a file for each of the microservices, but using Helm we can define a common blueprint for all the microservices and replace the dynamic values for placeholders, that comes usually from an external file named *values.yaml*.

It is also usefull when deploying the same application among diffrent environments we can make our application chart that are necessary for the application, and use them to redeploy in different clusters using one command.

## Helm chart structure

The directory structure would be:

```sh
mychart/
  Chart.yaml
  values.yaml
  charts/
  templates/
```

In the top level mychart folder, there would be the name of the chart.
In the Chart.yaml would be the meta info about the chart.
IN the values files we would find all the values for the template files.
charts folder would be multiple chart depeendencies, aind finally, templates would be all the templates that are filed using values.

To install we should run

```sh
helm install [chartname]
```

Values from values.yaml are inserted in the templates but they can be overwrited using the values flag on the cli:
```sh
helm install --values=my-values.yaml [chartname]
```

Another feature is the Release Management, having a big difference between Helm v2 and v3.
In the first the installation is split in two parts:

1. Client (helm CLI)
2. Server (Tiller)

Whenever we create or change a deployment, Tiller keeps a a list of the chart executions, this is also good to handle rollbacks.
However there is a problem, Tiller has too much power (permissions) inside the K8s cluster, that's why in v3 the Tiller part was removed.


