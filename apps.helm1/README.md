# app.heml1

<https://github.com/moisesjurad0/argoCD-app-of-apps-definition>

<https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/>

Chart.yml is empty

## Command Summary

1. exec this:

    ```PowerShell
        argocd app create appsh1 \
        --dest-namespace argocd \
        --dest-server https://kubernetes.default.svc \
        --repo https://github.com/moisesjurad0/argoCD-app-of-apps-definition \
        --path apps.helm1
                
        argocd app sync apps
    ```
