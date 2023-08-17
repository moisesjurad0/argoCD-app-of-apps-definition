# app.heml1

<https://github.com/moisesjurad0/argoCD-app-of-apps-definition>

<https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/>

Chart.yml is empty

## Command Summary

1. login to server using this:

    ```PowerShell
    #argocd login cd.apps.argoproj.io
    argocd login 127.0.0.1:8080
    ```

    output:

    ```PowerShell
    argocd login 127.0.0.1:8080                                                                               pwsh   100  11:53:00 
    WARNING: server certificate had error: tls: failed to verify certificate: x509: certificate signed by unknown authority. Proceed insecurely (y/n)? y
    Username: admin
    Password:
    'admin:login' logged in successfully
    Context '127.0.0.1:8080' updated    
    ```

1. then create the app:

    ```PowerShell
    argocd app create apph1 `
    --dest-namespace argocd `
    --dest-server https://kubernetes.default.svc `
    --repo https://github.com/moisesjurad0/argoCD-app-of-apps-definition `
    --path apps.helm1 `
    --server 127.0.0.1:8080
    ```

    output:

    ```PowerShell
    application 'apph1' created   
    ```

1. then sync the app:

    ```PowerShell
    argocd app sync apph1 --server localhost:8080
    ```

    output:

    ```PowerShell
    # asdasdasd
    ```
