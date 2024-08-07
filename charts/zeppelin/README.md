# Apache Zeppelin

A Zeppelin helm chart for Kubernetes

## Install

To install the chart with a release name `my-notebooks`:
```
$ helm repo add kubelake https://esolutionstech.github.io/kubelakecharts
$ helm install --version 1.0.0 my-notebooks kubelake/zeppelin
```
To uninstall the release
```
$ helm delete my-notebooks
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| autoscaling.enabled | bool | `false` |  |
| autoscaling.maxReplicas | int | `100` |  |
| autoscaling.minReplicas | int | `1` |  |
| autoscaling.targetCPUUtilizationPercentage | int | `80` |  |
| env.FLINK_HOME | string | `"/opt/flink"` |  |
| env.SPARK_HOME | string | `"/spark"` |  |
| env.SPARK_MASTER | string | `"k8s://https://kubernetes.default.svc"` |  |
| env.ZEPPELIN_HOME | string | `"/opt/zeppelin"` |  |
| env.ZEPPELIN_K8S_CONTAINER_IMAGE | string | `"ghcr.io/esolutionstech/kubelake-zeppelin-server:0.11.1"` |  |
| env.ZEPPELIN_K8S_SPARK_CONTAINER_IMAGE | string | `"ghcr.io/esolutionstech/kubelake-spark:3.5.1"` |  |
| env.ZEPPELIN_PORT | int | `8080` |  |
| env.ZEPPELIN_SERVER_RPC_PORTRANGE | string | `"12320:12320"` |  |
| envFrom | list | `[]` | Extra env from ConfigMap or Secret |
| extraEnv | list | `[]` | Extra env variables to pass to the container |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"apache/zeppelin"` |  |
| image.tag | string | `"0.11.1"` |  |
| imagePullSecrets | list | `[]` |  |
| ingress.annotations | object | `{}` |  |
| ingress.enabled | bool | `false` |  |
| ingress.hosts[0].host | string | `"chart-example.local"` |  |
| ingress.hosts[0].paths[0] | string | `"/"` |  |
| ingress.tls | list | `[]` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podAnnotations | object | `{}` |  |
| podSecurityContext | object | `{}` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| service.port | int | `80` |  |
| service.type | string | `"ClusterIP"` |  |
| serviceAccount.annotations | object | `{}` |  |
| serviceAccount.create | bool | `true` |  |
| serviceAccount.name | string | `""` |  |
| tolerations | list | `[]` |  |
| volumeMounts | list | `[]` | Volume mounts |
| volumes | list | `[]` | Volumes to be mounted |
----------------------------------------------
