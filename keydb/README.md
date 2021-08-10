# KeyDB

[KeyDB](https://keydb.dev) clocks in at 5X faster than Redis (node vs node). KeyDB is a popular drop in Redis alternative that people flock to because it enables you to consolidate a lot of the complexities associated with Redis. KeyDB is multithreaded with the ability to use several storage mediums natively and scale vertically. The superior architecture is enabling KeyDB to become the bridge between cache layer and traditional databases offering performance and durability.

## TL;DR;

```bash
helm repo add enapter https://enapter.github.io/charts/
helm install keydb enapter/keydb
```

## Introduction

This chart bootstraps a [KeyDB](https://keydb.dev) highly available multi-master statefulset in a [Kubernetes](http://kubernetes.io) cluster using the Helm package manager.

## Prerequisites

- PV provisioner support in the underlying infrastructure if you want to enable persistence

## Installing the Chart

To install the chart

```bash
helm repo add enapter https://enapter.github.io/charts/
helm install keydb enapter/keydb
```

## Configuration

The following table lists the configurable parameters of the KeyDB chart and their default values.

| Parameter                       | Description                                        | Default                                   |
|:--------------------------------|:---------------------------------------------------|:------------------------------------------|
| `image`                         | KeyDB docker image                                 | `eqalpha/keydb:x86_64_v6.0.18`            |
| `imagePullPolicy`               | K8s imagePullPolicy                                | `IfNotPresent`                            |
| `nodes`                         | Number of KeyDB master pods                        | `3`                                       |
| `password`                      | If enabled KeyDB servers are password-protected    | `""`                                      |
| `existingSecret`                | If enabled password is taken from secret           | `""`                                      |
| `port`                          | KeyDB service port clients connect to              | `6379`                                    |
| `threads`                       | KeyDB server-threads per node                      | `2`                                       |
| `multiMaster`                   | KeyDB multi-master setup                           | `yes`                                     |
| `activeReplicas`                | KeyDB active replication setup                     | `yes`                                     |
| `protectedMode`                 | KeyDB protection mode                              | `no`                                      |
| `appendonly`                    | KeyDB appendonly setting                           | `no`                                      |
| `configExtraArgs`               | Additional configuration arguments for KeyDB       | `{}`                                      |
| `podAnnotations`                | KeyDB pods annotations                             | `{}`                                      |
| `tolerations`                   | KeyDB tolerations setting                          | `{}`                                      |
| `additionalAffinities`          | Additional affinities for StatefulSet              | `{}`                                      |
| `extraInitContainers`           | Additional init containers for StatefulSet         | `[]`                                      |
| `extraContainers`               | Additional sidecar containers for StatefulSet      | `[]`                                      |
| `extraVolumes`                  | Additional volumes for init and sidecar containers | `[]`                                      |
| `livenessProbe`                 | LivenessProbe for KeyDB pods                       | Look values.yaml                          |
| `readinessProbe`                | ReadinessProbe for KeyDB pods                      | Look values.yaml                          |
| `startupProbe`                  | StartupProbe for KeyDB pods                        | Look values.yaml                          |
| `persistentVolume.enabled`      | Should PVC be created via volumeClaimTemplates     | `true`                                    |
| `persistentVolume.accessModes`  | Volume access modes                                | `[ReadWriteOnce]`                         |
| `persistentVolume.size`         | Size of the volume                                 | `1Gi`                                     |
| `persistentVolume.storageClass` | StorageClassName for volume                        | ``                                        |
| `resources`                     | Resources for KeyDB containers                     | `{}`                                      |
| `securityContext`               | SecurityContext for KeyDB pods                     | `{}`                                      |
| `service.annotations`           | Service annotations                                | `{}`                                      |
| `loadBalancer.enabled`          | Create LoadBalancer service                        | `false`                                   |
| `loadBalancer.annotations`      | Annotations for LB                                 | `{}`                                      |
| `loadBalancer.extraSpec`        | Additional spec for LB                             | `{}`                                      |
| `serviceMonitor.enabled`        | Prometheus operator ServiceMonitor                 | `false`                                   |
| `serviceMonitor.labels`         | Additional labels for ServiceMonitor               | `{}`                                      |
| `serviceMonitor.annotations`    | Additional annotations for ServiceMonitor          | `{}`                                      |
| `serviceMonitor.interval`       | ServiceMonitor scrape interval                     | `30s`                                     |
| `serviceMonitor.scrapeTimeout`  | ServiceMonitor scrape timeout                      | `nil`                                     |
| `exporter.enabled`              | Prometheus Exporter sidecar contaner               | `false`                                   |
| `exporter.image`                | Exporter Image                                     | `oliver006/redis_exporter:v1.12.1-alpine` |
| `exporter.pullPolicy`           | Exporter imagePullPolicy                           | `IfNotPresent`                            |
| `exporter.port`                 | `prometheus.io/port`                               | `9121`                                    |
| `exporter.scrapePath`           | `prometheus.io/path`                               | `/metrics`                                |
| `exporter.livenessProbe`        | LivenessProbe for sidecar Prometheus exporter      | Look values.yaml                          |
| `exporter.readinessProbe`       | ReadinessProbe for sidecar Prometheus exporter     | Look values.yaml                          |
| `exporter.startupProbe`         | StartupProbe for sidecar Prometheus exporter       | Look values.yaml                          |
| `exporter.resources`            | Resources for sidecar Prometheus container         | `{}`                                      |
| `exporter.extraArgs`            | Additional arguments for exporter                  | `{}`                                      |

## Using existingSecret

When definining existingSecret (by default is "") password value is ignored. Password is taken from that secret, instead of being provided as plain text under values.yaml file. \
Secret key must be *password*. \
Example of of such secret: 
```bash
kubectl create secret generic keydb-password --from-literal=password=KEYDB_PASSWORD
```
Definition of existingSecret in that case:
```yaml
password: ""
existingSecret: keydb-password
```
It is important to use only one way of providing passwords: via plain text under values.yaml or using already existing secret.
