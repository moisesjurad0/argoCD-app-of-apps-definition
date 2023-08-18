# GitOps Aplications

<https://github.com/moisesjurad0/argoCD-app-of-apps-definition>

1. app-root <-- app de apps, la principal es basada en repo git y las secundarias pueden ser repo git pero también repo Helm
1. apph1 <-- app de apps basada en Helm Chart usando la carpeta templates de Helm
1. apph2 <-- app de apps basada en Helm Chart pero funciona como una unificada xq tiene dependencias

## Command Summary

1. `kubectl apply -f .\apph2.yaml` <-- apply with kubectl the ArgoCD app. Then sync any changes.

1. `helm create <nombre de la app>` <-- puedes usar esto para crear una helm chart vacia y mirar sus parámetros
