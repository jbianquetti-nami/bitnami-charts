# PostgreSQL

[PostgreSQL](https://www.postgresql.org/) is an object-relational database management system (ORDBMS) with an emphasis on extensibility and on standards-compliance.

## TL;DR;

```bash
$ helm install postgresql-x.x.x.tgz
```

## Introduction

Bitnami charts for Helm are carefully engineered, actively maintained and are the quickest and easiest way to deploy containers on a Kubernetes cluster that are ready to handle production workloads.

This chart bootstraps a [PostgreSQL](https://github.com/bitnami/bitnami-docker-postgresql) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Get this chart

Download the latest release of the chart from the [releases](../../../releases) page.

Alternatively, clone the repo if you wish to use the development snapshot:

```bash
$ git clone https://github.com/bitnami/charts.git
```

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release postgresql-x.x.x.tgz
```

*Replace the `x.x.x` placeholder with the chart release version.*

The command deploys PostgreSQL on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following tables lists the configurable parameters of the PostgreSQL chart and their default values.

|      Parameter       |          Description           |                         Default                         |
|----------------------|--------------------------------|---------------------------------------------------------|
| `imageTag`           | `bitnami/postgresql` image tag | PostgreSQL image version                                |
| `imagePullPolicy`    | Image pull policy              | `Always` if `imageTag` is `latest`, else `IfNotPresent` |
| `postgresqlUsername` | PostgreSQL admin user          | `postgres`                                              |
| `postgresqlPassword` | PostgreSQL password            | `nil`                                                   |
| `postgresqlDatabase` | Database to create             | `nil`                                                   |

The above parameters map to the env variables defined in [bitnami/postgresql](http://github.com/bitnami/bitnami-docker-postgresql). For more information please refer to the [bitnami/postgresql](http://github.com/bitnami/bitnami-docker-postgresql) image documentation.

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
  --set postgresPassword=secretpassword,postgresDb=my-database \
    postgresql-x.x.x.tgz
```

The above command sets the PostgreSQL `postgres` account password to `secretpassword`. Additionally it creates a database named `my-database`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml postgresql-x.x.x.tgz
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Persistence

The [Bitnami PostgreSQL](https://github.com/bitnami/bitnami-docker-postgresql) image stores the PostgreSQL data and configurations at the `/bitnami/postgresql` path of the container.

As a placeholder, the chart mounts an [emptyDir](http://kubernetes.io/docs/user-guide/volumes/#emptydir) volume at this location.

> *"An emptyDir volume is first created when a Pod is assigned to a Node, and exists as long as that Pod is running on that node. When a Pod is removed from a node for any reason, the data in the emptyDir is deleted forever."*

For persistence of the data you should replace the `emptyDir` volume with a persistent [storage volume](http://kubernetes.io/docs/user-guide/volumes/), else the data will be lost if the Pod is shutdown.

### Step 1: Create a persistent disk

You first need to create a persistent disk in the cloud platform your cluster is running. For example, on GCE you can use the `gcloud` tool to create a [gcePersistentDisk](http://kubernetes.io/docs/user-guide/volumes/#gcepersistentdisk):

```bash
$ gcloud compute disks create --size=500GB --zone=us-central1-a postgresql-data-disk
```

### Step 2: Update `templates/deployment.yaml`

Replace:

```yaml
      volumes:
      - name: postgresql-data
        emptyDir: {}
```

with

```yaml
      volumes:
      - name: postgresql-data
        gcePersistentDisk:
          pdName: postgresql-data-disk
          fsType: ext4
```

[Install](#installing-the-chart) the chart after making these changes.
