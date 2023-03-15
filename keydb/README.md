# KeyDB

[KeyDB](https://keydb.dev) clocks in at 5X faster than Redis (node vs node). KeyDB is a popular drop in Redis alternative that people flock to because it enables you to consolidate a lot of the complexities associated with Redis. KeyDB is multithreaded with the ability to use several storage mediums natively and scale vertically. The superior architecture is enabling KeyDB to become the bridge between cache layer and traditional databases offering performance and durability.

## TL;DR;

```bash
helm repo add enapter https://enapter.github.io/charts/
helm install keydb enapter/keydb
```

## Introduction

This chart bootstraps a [KeyDB](https://keydb.dev) highly available multi-master statefulset in a [Kubernetes](http://kubernetes.io) cluster using the Helm package manager.

## 0.40.1 Upgrade notice

As the chart is not yet production ready (0.x) backward incompatible changes can be introduced in minor releases.

`exporter.pullPolicy` is deprecated in favor of `exporter.imagePullPolicy`


## 0.38.0 Upgrade notice

As the chart is not yet production ready (0.x) backward incompatible changes can be introduced in minor releases.

This release enables using a dedicated ServiceAccount for the KeyDB StatefulSet. Either an SA created by the chart or a pre-exising SA can be used. The corresponding value setting `serviceAccount.enabled` is turned off by default for backward compatibility.

Please note that the `serviceAccountName` field of the StatefulSet's spec is immutable, so an upgrade from a helm release where the dedicated SA is disabled (the default) to a release where it is explicitly enabled is impossible and will fail. You should plan a migration to an SA-enabled release in advance considering your environment and operational practices, e.g. using a blue-green deployment or scheduling a downtime for removal of the previous release. In case of removal please also consider data retention as necessary, e.g. verify the reclaim policy of the StorageClass in use.

If you plan a deployment in an environment where dedicated ServiceAccounts are essential, e.g. in a service mesh, please consider enabling the SA setting from the start.

## 0.33.0 Upgrade notice

As the chart is not yet production ready (0.x) backward incompatible changes can be introduced in minor releases.
Since 0.33.0 `scripts.cleanup` is obsoleted by `scripts.cleanupTempfiles`. `scripts.cleanupCoredumps` section is added in order to provide ability to cleanup `core.*` files and is disabled by default. Please look `values.yaml`.

## 0.30.0 Upgrade notice

As the chart is not yet production ready (0.x) backward incompatible changes can be introduced in minor releases.
Since 0.30.0 `additionalAffinities` option is completely obsolete and `affinity` replaces it. `affinity` is rendered dynamically (approach is taken from Bitnami charts) so you can set dynamic things like `'{{ .Release.Name }}'` right inside the `affinity:` key in `values.yaml`. Will extend this approach for other places in the future.

## 0.29.0 Upgrade notice

As the chart is not yet production ready (0.x) backward incompatible changes can be introduced in minor releases.
Since 0.29.0 `configExtraArgs` and `exporter.extraArgs` are now arrays of dicts in order to allow repeating arguments with the same key.
If dict value is an array it is interpreted as multiple arguments for the key.

### Config Example:

```
configExtraArgs:
  - client-output-buffer-limit: ["normal", "0", "0", "0"]
  - client-output-buffer-limit: ["replica", "268435456", "67108864", "60"]
  - client-output-buffer-limit: ["pubsub", "33554432", "8388608", "60"]
  - save: ~
  - tcp-backlog "1024"
```

### Resulting File:

```
...

exec keydb-server /etc/keydb/redis.conf \

    ...

    --client-output-buffer-limit "normal" "0" "0" "0" \
    --client-output-buffer-limit "replica" "268435456" "67108864" "60" \
    --client-output-buffer-limit "pubsub" "33554432" "8388608" "60" \
    --save \
    --tcp-backlog "1024" \

    ...
```

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
| `imageRepository`               | KeyDB docker image                                 | `eqalpha/keydb`                           |
| `imageTag`                      | KeyDB docker image tag                             | `x86_64_v6.3.2`                           |
| `imagePullPolicy`               | K8s imagePullPolicy                                | `IfNotPresent`                            |
| `imagePullSecrets`              | KeyDB Pod imagePullSecrets                         | `[]`                                      |
| `nodes`                         | Number of KeyDB master pods                        | `3`                                       |
| `password`                      | If enabled KeyDB servers are password-protected    | `""`                                      |
| `existingSecret`                | If enabled password is taken from secret           | `""`                                      |
| `existingSecretPasswordKey`     | Secret key name.                                   | `"password"`                              |
| `port`                          | KeyDB service port clients connect to              | `6379`                                    |
| `portName`                      | KeyDB service port name in the Service spec        | `server`                                  |
| `threads`                       | KeyDB server-threads per node                      | `2`                                       |
| `multiMaster`                   | KeyDB multi-master setup                           | `yes`                                     |
| `activeReplicas`                | KeyDB active replication setup                     | `yes`                                     |
| `protectedMode`                 | KeyDB protection mode                              | `no`                                      |
| `appendonly`                    | KeyDB appendonly setting                           | `no`                                      |
| `configExtraArgs`               | Additional configuration arguments for KeyDB       | `[]`                                      |
| `annotations`                   | KeyDB StatefulSet annotations                      | `{}`                                      |
| `podAnnotations`                | KeyDB pods annotations                             | `{}`                                      |
| `tolerations`                   | KeyDB tolerations setting                          | `{}`                                      |
| `nodeSelector`                  | KeyDB nodeSelector setting                         | `{}`                                      |
| `topologySpreadConstraints`     | KeyDB topologySpreadConstraints setting            | `[]`                                      |
| `affinity`                      | StatefulSet Affinity rules                         | Look values.yaml                          |
| `extraInitContainers`           | Additional init containers for StatefulSet         | `[]`                                      |
| `extraContainers`               | Additional sidecar containers for StatefulSet      | `[]`                                      |
| `extraVolumes`                  | Additional volumes for init and sidecar containers | `[]`                                      |
| `livenessProbe.custom`          | Custom LivenessProbe for KeyDB pods                | `{}`                                      |
| `readinessProbe.custom`         | Custom ReadinessProbe for KeyDB pods               | `{}`                                      |
| `readinessProbeRandomUuid`      | Random UUIDv4 for readiness GET probe              | `90f717dd-0e68-43b8-9363-fddaad00d6c9`    |
| `startupProbe.custom`           | Custom StartupProbe for KeyDB pods                 | `{}`                                      |
| `persistentVolume.enabled`      | Should PVC be created via volumeClaimTemplates     | `true`                                    |
| `persistentVolume.accessModes`  | Volume access modes                                | `[ReadWriteOnce]`                         |
| `persistentVolume.selector`     | PVC selector. (In order to match existing PVs)     | `{}`                                      |
| `persistentVolume.size`         | Size of the volume                                 | `1Gi`                                     |
| `persistentVolume.storageClass` | StorageClassName for volume                        | ``                                        |
| `podDisruptionBudget`           | podDisruptionBudget for KeyDB pods                 | Look values.yaml                          |
| `resources`                     | Resources for KeyDB containers                     | `{}`                                      |
| `scripts.enabled`               | Turn on health util scripts                        | `false`                                   |
| `scripts.cleanupCoredumps`      | Coredumps cleanup scripts                          | Look values.yaml                          |
| `scripts.cleanupTempfiles`      | Tempfiles cleanup scripts                          | Look values.yaml                          |
| `scripts.securityContext`       | SecurityContext for scripts container              | `{}`                                      |
| `keydb.securityContext`         | SecurityContext for KeyDB container                | `{}`                                      |
| `securityContext`               | SecurityContext for KeyDB pods                     | `{}`                                      |
| `service.annotations`           | Service annotations                                | `{}`                                      |
| `service.appProtocol.enabled`   | Turn on appProtocol fields in port specs           | `false`                                   |
| `loadBalancer.enabled`          | Create LoadBalancer service                        | `false`                                   |
| `loadBalancer.annotations`      | Annotations for LB                                 | `{}`                                      |
| `loadBalancer.extraSpec`        | Additional spec for LB                             | `{}`                                      |
| `serviceAccount.enabled`        | Use a dedicated ServiceAccount (SA)                | `false`                                   |
| `serviceAccount.create`         | Create the SA (rather than use an existing one)    | `true`                                    |
| `serviceAccount.name`           | Set the name of an existing SA or override created | ``                                        |
| `serviceAccount.extraSpec`      | Additional spec for the created SA                 | `{}`                                      |
| `serviceMonitor.enabled`        | Prometheus operator ServiceMonitor                 | `false`                                   |
| `serviceMonitor.labels`         | Additional labels for ServiceMonitor               | `{}`                                      |
| `serviceMonitor.annotations`    | Additional annotations for ServiceMonitor          | `{}`                                      |
| `serviceMonitor.interval`       | ServiceMonitor scrape interval                     | `30s`                                     |
| `serviceMonitor.scrapeTimeout`  | ServiceMonitor scrape timeout                      | `nil`                                     |
| `exporter.enabled`              | Prometheus Exporter sidecar contaner               | `false`                                   |
| `exporter.imageRepository`      | Exporter Image                                     | `oliver006/redis_exporter`                |
| `exporter.imageTag`             | Exporter Image Tag                                 | `v1.48.0-alpine`                          |
| `exporter.pullPolicy`           | Exporter imagePullPolicy                           | `IfNotPresent`                            |
| `exporter.port`                 | `prometheus.io/port`                               | `9121`                                    |
| `exporter.portName`             | Exporter service port name in the Service spec     | `redis-exporter`                          |
| `exporter.scrapePath`           | `prometheus.io/path`                               | `/metrics`                                |
| `exporter.livenessProbe`        | LivenessProbe for sidecar Prometheus exporter      | Look values.yaml                          |
| `exporter.readinessProbe`       | ReadinessProbe for sidecar Prometheus exporter     | Look values.yaml                          |
| `exporter.startupProbe`         | StartupProbe for sidecar Prometheus exporter       | Look values.yaml                          |
| `exporter.resources`            | Resources for sidecar Prometheus container         | `{}`                                      |
| `exporter.securityContext`      | SecurityContext for Prometheus exporter container  | `{}`                                      |
| `exporter.extraArgs`            | Additional arguments for exporter                  | `[]`                                      |

## Using existingSecret

When definining existingSecret (by default is "") password value is ignored. Password is taken from that secret, instead of being provided as plain text under values.yaml file. \
Secret key must be `existingSecretPasswordKey` (*password* by default). \
Example of of such secret: 
```bash
kubectl create secret generic keydb-password --from-literal=password=KEYDB_PASSWORD
```
Definition of existingSecret in that case:
```yaml
password: ""
existingSecret: keydb-password
existingSecretPasswordKey: password-key-in-secret-file
```
It is important to use only one way of providing passwords: via plain text under values.yaml or using already existing secret.
