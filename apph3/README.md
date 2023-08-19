# argoCD-app-of-apps-definition

<https://github.com/moisesjurad0/argoCD-app-of-apps-definition>

## Command Summary

1. `kubectl apply -f .\apph3.yaml` <-- apply with kubectl the ArgoCD app. Then sync any changes.
2. or instead, can use a modified version of this

    ```PowerShell
        argocd app create apps \
        --dest-namespace argocd \
        --dest-server https://kubernetes.default.svc \
        --repo https://github.com/argoproj/argocd-example-apps.git \
        --path apps
        
        # then sync
        argocd app sync apps  
        # or sync this way using minikube
        argocd app sync apps --server localhost:8080
    ```
