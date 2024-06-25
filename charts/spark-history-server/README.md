# Spark-History-Server

A Helm chart for the Spark-History-Server

## Introduction

This chart bootstraps a [Spark History Server](https://spark.apache.org/docs/latest/monitoring.html#viewing-after-the-fact) deployment using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Helm >= 3
- Kubernetes >= 1.19

## Installing the chart

To deploy the chart with a release name `my-release`:

1. First, add the kubelake helm charts repository:

```shell
$ helm repo add kubelake https://esolutionstech.github.io/kubelakecharts
```

2. Then, install the chart with the release name `my-release`:

```shell
$ helm install --version 1.0.0 \
  my-release kubelake/spark-history-server
```

This will create a release of `my-release` in the default namespace. To install in a different one:

```shell
$ helm install --version 1.0.0 \
  --namespace spark \
  my-release kubelake/spark-history-server
```

Note that `helm` will fail to install if the namespace doesn't exist. Either create the namespace beforehand or pass the `--create-namespace` flag to the `helm install` command.

## Uninstalling the chart  `my-release`

To uninstall `my-release`:

```shell
$ helm uninstall my-release -n spark
```

The command removes all the Kubernetes components associated with the chart and deletes the release.
## Configuration examples

### Using an S3-Compatible System for Log Storage

This example guides you through setting up the Spark History Server to store logs in an S3-compatible storage system.

#### Authentication using accessKey and secretKey

##### 1. Create a Kubernetes Secret for S3 Credentials

This secret will store the access key and secret key required to access your S3 bucket.

```shell
kubectl create secret generic s3-secret \
  --from-literal=accessKey='XXXXX' \
  --from-literal=secretKey='XXXXX' \
  -n spark
```

Replace your-access-key and your-secret-key with your actual S3 credentials.

##### 2. Prepare Configuration Values (my-values.yaml):

```yaml
config:
  spark.hadoop.fs.s3a.endpoint: https://<S3 endpoint>
  spark.hadoop.fs.s3a.connection.ssl.enabled: true
  spark.history.fs.logDirectory: s3a://<your-bucket-name>/eventLogs
  spark.hadoop.fs.s3a.impl: org.apache.hadoop.fs.s3a.S3AFileSystem
  spark.hadoop.fs.s3a.path.style.access: true

extraEnvs:
  - name: AWS_ACCESS_KEY_ID
    valueFrom:
      secretKeyRef:
        key: accessKey
        name: s3-secret
  - name: AWS_SECRET_ACCESS_KEY
    valueFrom:
      secretKeyRef:
        key: secretKey
        name: s3-secret
  # Disable Certificate Checking
  - name: SPARK_HISTORY_OPTS
    value: "-Dcom.amazonaws.sdk.disableCertChecking=true"
```

##### 3. Deploy the Helm Chart

```shell
helm repo add kubelake https://esolutionstech.github.io/kubelakecharts
helm install --version 1.0.0 \
  --namespace spark \
  --values my-values.yaml \
  my-release kubelake/spark-history-server
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| commonAnnotations | object | `{}` | Common annotations for all deployed resources. Useful for tools that perform actions based on annotations. |
| commonLabels | object | `{}` | Common labels for all deployed resources. Use this to apply a set of labels to all resources that are deployed. |
| config."spark.history.custom.executor.log.url" | string | `""` | Specifies custom spark executor log URL for supporting external log service. |
| config."spark.history.custom.executor.log.url.applyIncompleteApplication" | bool | `true` | Specifies whether to apply custom spark executor log URL to incomplete applications as well. |
| config."spark.history.fs.cleaner.enabled" | bool | `false` | Specifies whether the History Server should periodically clean up event logs from storage. |
| config."spark.history.fs.cleaner.interval" | string | `"1d"` | Specifies how often the filesystem job history cleaner checks for files to delete. |
| config."spark.history.fs.cleaner.maxAge" | string | `"7d"` | Job history files older than this will be deleted when the cleaner runs. |
| config."spark.history.fs.cleaner.maxNum" | int | `2147483647` | Specifies the maximum number of files in the event log directory (defaults to Int.MaxValue). |
| config."spark.history.fs.driverlog.cleaner.enabled" | string | `"spark.history.fs.cleaner.enabled"` | Specifies whether the History Server should periodically clean up driver logs. |
| config."spark.history.fs.driverlog.cleaner.interval" | string | `"spark.history.fs.cleaner.interval"` | Specifies how often the filesystem driver log cleaner checks for files to delete. |
| config."spark.history.fs.driverlog.cleaner.maxAge" | string | `"spark.history.fs.cleaner.maxAge"` | Driver log files older than this will be deleted when the cleaner runs. |
| config."spark.history.fs.endEventReparseChunkSize" | string | `"1m"` | How many bytes to parse at the end of log files looking for the end event. |
| config."spark.history.fs.eventLog.rolling.maxFilesToRetain" | int | `2147483647` | The maximum number of event log files which will be retained as non-compacted (defaults to Int.MaxValue). |
| config."spark.history.fs.inProgressOptimization.enabled" | bool | `true` | Enable optimized handling of in-progress logs. |
| config."spark.history.fs.logDirectory" | string | `"file:/tmp/spark-events"` | For the filesystem history provider, the URL to the directory containing application event logs to load. |
| config."spark.history.fs.update.batchSize" | int | `2147483647` | Specifies the batch size for updating new eventlog files (defaults to Int.MaxValue). |
| config."spark.history.fs.update.interval" | string | `"10s"` | The period at which the filesystem history provider checks for new or updated logs. |
| config."spark.history.kerberos.enabled" | bool | `false` | Indicates whether the history server should use kerberos to login. |
| config."spark.history.kerberos.keytab" | string | `""` | Location of the kerberos keytab file for the History Server when kerberos is enabled. |
| config."spark.history.kerberos.principal" | string | `""` | Kerberos principal name for the History Server when kerberos is enabled. |
| config."spark.history.provider" | string | `"org.apache.spark.deploy.history.FsHistoryProvider"` | Name of the class implementing the application history backend. |
| config."spark.history.retainedApplications" | int | `50` | The number of applications to retain UI data for in the cache. |
| config."spark.history.store.hybridStore.diskBackend" | string | `"ROCKSDB"` | Specifies a disk-based store used in hybrid store. |
| config."spark.history.store.hybridStore.enabled" | bool | `false` | Whether to use HybridStore as the store when parsing event logs. |
| config."spark.history.store.hybridStore.maxMemoryUsage" | string | `"2g"` | Maximum memory space for HybridStore. |
| config."spark.history.store.maxDiskUsage" | string | `"10g"` | Maximum disk usage for the local directory storing cached application history information. |
| config."spark.history.store.path" | string | `""` | Local directory where to cache application history data. |
| config."spark.history.store.serializer" | string | `"JSON"` | Serializer for writing/reading UI objects to/from disk-based KV Store. |
| config."spark.history.ui.maxApplications" | int | `2147483647` | The number of applications to display on the history summary page (defaults to Int.MaxValue). |
| config."spark.history.ui.port" | int | `18080` | The port to which the web interface of the history server binds. |
| containerSecurityContext | object | `{"allowPrivilegeEscalation":false,"capabilities":{"drop":["ALL"]},"readOnlyRootFilesystem":false,"runAsUser":185}` | Security context for the container. |
| deploymentName | string | `nil` | Desired name of the Spark History Server deployment. |
| envFrom | list | `[]` | Environment variables from ConfigMap or Secret. |
| extraEnvs | list | `[]` | Extra environment variables to pass to the container. |
| extraVolumeMounts | list | `[]` | Additional volume mounts. |
| extraVolumes | list | `[]` | Additional volumes to be mounted. |
| fullnameOverride | string | `""` | Overrides the release name. |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy. |
| image.repository | string | `"ghcr.io/esolutionstech/kubelake-spark"` | Docker image registry. |
| image.tag | string | `"3.5.1"` | Image tag. |
| imagePullSecrets | list | `[]` | Secrets to be used for pulling images from private Docker registries. |
| ingress.annotations | object | `{}` |  |
| ingress.enabled | bool | `false` |  |
| ingress.hosts[0].host | string | `"chart-example.local"` |  |
| ingress.hosts[0].paths[0].path | string | `"/"` |  |
| ingress.hosts[0].paths[0].pathType | string | `"Prefix"` |  |
| ingress.ingressClassName | string | `""` | Specify the ingress class (Kubernetes >= 1.18). |
| ingress.labels | object | `{}` |  |
| ingress.name | string | `nil` |  |
| ingress.tls | list | `[]` |  |
| lifecycle | object | `{}` |  |
| livenessProbe | object | `{}` | Liveness probe for the Spark History Server container. |
| logLevel | string | `"info"` | Log4j2 log level. One of `` `debug`, `info`, `warn`, `error`, `fatal`, `trace` |
| nameOverride | string | `""` | Override for the `spark-history-server.fullname` template, maintains the release name. |
| nodeSelector | object | `{}` |  |
| podAnnotations | object | `{}` | Additional annotations for the Spark History Server pod. |
| podLabels | object | `{}` | Additional labels for the Spark History Server pod. |
| podSecurityContext | object | `{"seccompProfile":{"type":"RuntimeDefault"}}` | Security context for the pod. |
| priorityClassName | string | `""` | Priority class name for the Spark History Server pod. |
| readinessProbe | object | `{}` | Readiness probe for the Spark History Server container. |
| replicaCount | int | `1` | Desired number of Spark History Server pods to run. |
| resources | object | `{}` |  |
| service.annotations | object | `{}` |  |
| service.httpPortName | string | `"http"` |  |
| service.labels | object | `{}` |  |
| service.loadBalancerSourceRanges | list | `[]` |  |
| service.name | string | `nil` |  |
| service.nodePort | string | `""` |  |
| service.port | int | `18080` |  |
| service.type | string | `"ClusterIP"` |  |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account. |
| serviceAccount.create | bool | `false` | Whether to create a service account or not. |
| serviceAccount.name | string | `"default"` | The name of the service account to use. |
| startupProbe | object | `{}` | Startup probe for the Spark History Server container. |
| tolerations | object | `{}` |  |
| topologySpreadConstraints | list | `[]` |  |
| updateStrategy | object | `{"type":"RollingUpdate"}` | Update strategy for deployments. |

