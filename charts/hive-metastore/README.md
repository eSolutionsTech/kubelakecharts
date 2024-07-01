# HMS Helm Chart
This Hive chart encapsulates all you need to deploy a Hive Metastore service in a Kubernetes cluster.

## Installation
Hive Metastore requires an external database. The PostgreSQL database can be configured with values:
```
postgres:
  enabled: true
  host: "postgresql"
  port: 5432
  db: metastore
  username: admin
  password: pass
``` 
or you can use another SQL Database. It can be configured with values e.g.:
```
postgres:
  enabled: false

extraEnvs:
  - name: DB_DRIVER
    value: mariadb  
  - name: DB
    values:"jdbc:mariadb://..."    
```

### Installing the chart
To install the chart with the release name my-hive:
```shell
$ helm repo add kubelake https://esolutionstech.github.io/kubelakecharts
$ helm install --version 1.0.0 my-hive kubelake/hive-metastore
```
To uninstall the release
```shell
$ helm delete my-hive 
```

### Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| fullnameOverride | string | `""` | Overrides the release name. |
| nameOverride | string | `""` | Override for the `spark-history-server.fullname` template, maintains the release name. |
| replicaCount | int | `1` | Desired number of HMS pods to run. |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy. |
| image.repository | string | `"apache/hive"` | Docker image registry. |
| image.tag | string | `"3.1.3"` | Image tag. |
| imagePullSecrets | list | `[]` | Secrets to be used for pulling images from private Docker registries. |
| postgres.enabled | bool | `true` | It uses PostgreSQL external database. |
| postgres.host | string | `"postgresql"` | |
| postgres.port | int | `5432` | |
| postgres.db | string | `"metastore"` | |
| postgres.username | string | `"admin"` | |
| postgres.password | string | `"pass"` | |
| envFrom | list | `[]` | Environment variables from ConfigMap or Secret. |
| extraEnvs | list | `[]` | Extra environment variables to pass to the container. |
| volumeMounts | list | `[]` | Volume mounts. |
| volumes | list | `[]` | Volumes to be mounted. |
----------------------------------------------