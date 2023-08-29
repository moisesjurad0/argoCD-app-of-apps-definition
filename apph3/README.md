# argoCD-app-of-apps-definition

<https://github.com/moisesjurad0/k8s-argoCD-app-of-apps-definition>

## Command Summary

1. run argo installation

    ```sh
    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```

1. `kubectl port-forward svc/argocd-server -n argocd 8080:443` <-- do a port-forward to access web interface
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
