# app.heml2 (apph2)

<https://github.com/moisesjurad0/k8s-argoCD-app-of-apps-definition>

<https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/>

Chart.yml is empty

## Command Summary

1. `kubectl apply -f .\apph2.yaml` <-- apply with kubectl the ArgoCD app. Then sync any changes.

1. `helm create <nombre de la app>` <-- puedes usar esto para crear una helm chart vacia y mirar sus parámetros
