# Automatically Roll Deployments with ArgoCD and Helm
Forked repository from [cm-example](https://github.com/christianh814/cm-example). This repository has been changed to use Helm for automatic rollout deployment when the config changes.

In this lab, the sample application has a **configmap**. The main idea is to roll the deployment when the config changes.

- [Automatically Roll Deployments with ArgoCD and Helm](#automatically-roll-deployments-with-argocd-and-helm)
  - [Prerequisites](#prerequisites)
  - [Sample app: Helm Chart](#sample-app-helm-chart)
  - [Update configmap](#update-configmap)
  - [Author](#author)
  
## Prerequisites

- OCP up and running.
- OpenShift GitOps Operator (ArgoCD)

## Sample app: Helm Chart
The sample app contains several Kubernetes resources. One of these resources is the **configmap**. Take a look at the content of the configmap:

```bash
helm template .

# Source: sample-app/templates/cm.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sample-app
  namespace: sample
data:
  test.conf: |
    title = "Example Config"
    enableAwesome = "true"
    version = "v1"
```

We see _version=v1_ in this case.

Let's deploy the application by using ArgoCD:

```bash
oc apply -n openshift-gitops -f app-helm.yaml
```

The app is running with the configuration indicated in the configmap.

Argo application           |  Sample app
:-------------------------:|:-------------------------:
<img src=./assets/images/0-app-argocd.png width=700>  |  <img src=./assets/images/1-sample-app.png width=700>

## Update configmap
Now it's time to update the configmap. Once the configmap is updated, the application will be rolled out automatically to load the new configmap.

First, edit the values in the configmap. Take a look at the git diff:

<img src=./assets/images/2-git-diff.png width=700>

Push the code. ArgoCD will refresh and sync:

<img src=./assets/images/3-argocd-cm-changed.png width=700>

See the **configmap** is _OutOfSync_, and also the deployment. Thus, the deployment will be rolled out.

Once Argocd finishes the Sync operation, the application is running with the new configmap:

Argo application           |  Sample app
:-------------------------:|:-------------------------:
<img src=./assets/images/4-argocd-new-version.png width=700>  |  <img src=./assets/images/5-sample-app.png width=700>

## Author

Fran Perea @RedHat