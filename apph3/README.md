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

Can find it here

<https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-mongodb-exporter>
