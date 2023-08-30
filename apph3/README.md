# argoCD-app-of-apps-definition

<https://github.com/moisesjurad0/k8s-argoCD-app-of-apps-definition>

## Command Summary

1. run argo installation

    ```sh
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

1. `kubectl port-forward svc/argocd-server -n argocd 8080:443` <-- do a port-forward to access web interface
    - open: <https://127.0.0.1:8080/>
    - user
    : admin
    - password
    : `argocd admin initial-password -n argocd` <-- use this to obtain password

1. `kubectl apply -f .\apph3.yaml` <-- apply with kubectl the ArgoCD app. Then sync any changes.
1. or instead, can use a modified version of this

    ```PowerShell
    argocd app create apps --dest-namespace argocd --dest-server https://kubernetes.default.svc --repo https://github.com/argoproj/argocd-example-apps.git --path apps
    
    # then sync
    argocd app sync apps  
    # or sync this way using minikube
    argocd app sync apps --server localhost:8080
    ```

1. `kubectl port-forward -n=pr-op service/pr-op-kube-prometheus-stac-prometheus 9090` <-- do a port-forward to access web interface
    - open: <http://127.0.0.1:9090>
        - go to: Status >> Targets

1. `kubectl get servicemonitor -n pr-op` <-- get all servicemonitor
1. `kubectl get servicemonitor pr-op-grafana -n pr-op -oyaml` <-- get all servicemonitor
1. `kubectl get crd -n pr-op`
1. `kubectl get prometheuses.monitoring.coreos.com -n pr-op -oyaml`

## solving problems

1. problem syncing helm prometheus

    this was the problem message

    ```txt
    Failed sync attempt to 49.2.0: one or more objects failed to apply, reason: CustomResourceDefinition.apiextensions.k8s.io "prometheusagents.monitoring.coreos.com" is invalid: metadata.annotations: Too long: must have at most 262144 bytes,CustomResourceDefinition.apiextensions.k8s.io "prometheuses.monitoring.coreos.com" is invalid: metadata.annotations: Too long: must have at most 262144 bytes,resource mapping not found for name: "pr-op-kube-prometheus-stac-prometheus" namespace: "pr-op" from "/dev/shm/2748583596": no matches for kind "Prometheus" in version "monitoring.coreos.com/v1" ensure CRDs are installed first
    ```

    or better cut off some words and search on google

    ```txt
    one or more objects failed to apply, reason: CustomResourceDefinition.apiextensions.k8s.io "prometheusagents.monitoring.coreos.com" is invalid: metadata.annotations: Too long: must have at most 262144 bytes,CustomResourceDefinition.apiextensions.k8s.io "prometheuses.monitoring.coreos.com" is invalid: metadata.annotations: Too long: must have at most 262144 bytes,resource mapping not found for name: "pr-op-kube-prometheus-stac-prometheus" namespace: "pr-op" from "/dev/shm/2748583596": no matches for kind "Prometheus" in version "monitoring.coreos.com/v1" ensure CRDs are installed first
    ```

    best result

    <https://github.com/prometheus-community/helm-charts/issues/3345>>

    ```txt
    Using ArgoCD, you need to enable ServerSideApply in the Application resource (see https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/#server-side-apply) for the CRDs to be applied properly.
    for kind "Prometheus" in version "monitoring.coreos.com/v1" ensure CRDs are installed first
    ```

    <https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/#server-side-apply>

    ```yaml
    syncPolicy:
        syncOptions:
        - ServerSideApply=true
    ```

    or maybe (because it worked when sync from web interface and checked REPLACE checkbox)

    ```yaml
    syncOptions:
        - Replace=true
    ```

## more instructions

`kubectl port-forward -n=pr-op service/pr-op-kube-prometheus-stac-prometheus 9090`

## Prometheus exporters

have 3 parts:

1. Exporter application <-- inside the docker image | exposes /metrics endpoint
1. Service (svc) <-- for connecting to the exporter
1. ServiceMonitor <-- to be discovered

### Prometheus exporters details

Can find it here

<https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-mongodb-exporter>

instruccions

```sh
# Get Repository Info
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install Chart
helm install [RELEASE_NAME] prometheus-community/prometheus-mongodb-exporter

# show values
helm show values prometheus-community/prometheus-mongodb-exporter

# save shown values
helm show values prometheus-community/prometheus-mongodb-exporter > values.yaml
```

show values output

```sh
affinity: {}

annotations: {}

## Additional labels to add to all resources
customLabels: {}

dnsConfig: {}

# Extra environment variables that will be passed into the exporter pod
env: {}

extraArgs:
  - --collect-all

fullnameOverride: ""

image:
  pullPolicy: IfNotPresent
  repository: percona/mongodb_exporter
  tag: ""

imagePullSecrets: []

livenessProbe:
  httpGet:
    path: /
    port: metrics
  initialDelaySeconds: 10

# [mongodb[+srv]://][user:pass@]host1[:port1][,host2[:port2],...][/database][?options]
mongodb:
  uri: "mongodb://mongodb:27017"

# Name of an externally managed secret (in the same namespace) containing the connection uri as key `mongodb-uri`.
# If this is provided, the value mongodb.uri is ignored.
existingSecret:
  name: ""
  key: "mongodb-uri"

nameOverride: ""

nodeSelector: {}

podAnnotations: {}
#  prometheus.io/scrape: "true"
#  prometheus.io/port: "metrics"

podLabels: {}

port: "9216"

priorityClassName: ""

readinessProbe:
  httpGet:
    path: /
    port: metrics
  initialDelaySeconds: 10

replicas: 1

resources: {}
  # limits:
  #   cpu: 250m
  #   memory: 192Mi
  # requests:
  #   cpu: 100m
  #   memory: 128Mi

securityContext:
  allowPrivilegeEscalation: false
  capabilities:
    drop: ["all"]
  readOnlyRootFilesystem: true
  runAsGroup: 10000
  runAsNonRoot: true
  runAsUser: 10000

service:
  labels: {}
  annotations: {}
  port: 9216
  type: ClusterIP
  portName: metrics

serviceAccount:
  create: true
  # If create is true and name is not set, then a name is generated using the
  # fullname template.
  name:

serviceMonitor:
  enabled: false
  interval: 30s
  scrapeTimeout: 10s
  namespace:
  additionalLabels: {}
  targetLabels: []
  metricRelabelings: []
  scheme: ""
  tlsConfig: {}

tolerations: []

# Volumes that will be attached to the exporter deployment
volumes: []

# Volumes that will be mounted into the exporter pod
volumeMounts: []
```

### Prometheus exporters | ServiceMonitor

ServiceMonitor is a special kind of services. It uses "labels relase prometheus" to get scrape from prometheus

`kubectl get servicemonitor -n mongodb mongodb-exporter-prometheus-mongodb-exporter -oyaml` <-- verify servicemonitor

```sh
kubectl get crd
# ouput
NAME                                        CREATED AT
alertmanagerconfigs.monitoring.coreos.com   2023-08-29T18:32:55Z
alertmanagers.monitoring.coreos.com         2023-08-29T18:32:56Z
applications.argoproj.io                    2023-08-29T18:23:16Z
applicationsets.argoproj.io                 2023-08-29T18:23:16Z
appprojects.argoproj.io                     2023-08-29T18:23:17Z
podmonitors.monitoring.coreos.com           2023-08-29T18:32:55Z
probes.monitoring.coreos.com                2023-08-29T18:32:55Z
prometheusagents.monitoring.coreos.com      2023-08-29T18:50:13Z
prometheuses.monitoring.coreos.com          2023-08-29T18:50:13Z
prometheusrules.monitoring.coreos.com       2023-08-29T18:32:55Z
scrapeconfigs.monitoring.coreos.com         2023-08-29T18:32:55Z
servicemonitors.monitoring.coreos.com       2023-08-29T18:32:55Z
thanosrulers.monitoring.coreos.com          2023-08-29T18:32:56Z
```

```sh
kubectl get crd prometheuses.monitoring.coreos.com -oyaml
kubectl get crd prometheuses.monitoring.coreos.com -oyaml > prometheuses.monitoring.coreos.com
# serviceMonitorSelector > matchLabels > release > prometheus
```
