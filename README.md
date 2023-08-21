# k8s - GitOps Application examples (ArgoCD)

<https://github.com/moisesjurad0/k8s-argoCD-app-of-apps-definition>

## App of Apps Examples

1. app-root <-- app de apps, la principal es basada en repo git y las secundarias pueden ser repo git pero también repo Helm
1. apph1 <-- app de apps basada en Helm Chart usando la carpeta templates de Helm
1. apph2 <-- app de apps basada en Helm Chart pero funciona como una unificada xq tiene dependencias
1. apph3 <-- app of apps based on apps folder with multiple apps defined in individual yaml file.
    1. one of the is a Helm chart
    1. the other is a folder containing multiple k8s yaml definition files from nana janashia's repo

## Command Summary

1. `kubectl apply -f .\apph2.yaml` <-- apply with kubectl the ArgoCD app. Then sync any changes.

1. `helm create <nombre de la app>` <-- puedes usar esto para crear una helm chart vacia y mirar sus parámetros
