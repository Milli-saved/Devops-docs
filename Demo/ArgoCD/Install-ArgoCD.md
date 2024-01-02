# Installation of ArgoCD
ArgoCD is a service that will be deployed on your Kubernetes cluster to manage application deployment and lifecycle.

## Installing on your kube cluster
To Install ArgoCD in your existing cluster run the following commands. or refer to their [documentation](https://argo-cd.readthedocs.io/en/stable/getting_started/)
```bash
kubectl create namespace kal-argocd 
kubectl apply -n kal-argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
## Install argocd cli on your local machine
To install ArgoCD cli on your local machine please see the [documentation](https://argo-cd.readthedocs.io/en/stable/cli_installation/) page. I preffered the Download with curl option.
```bash
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64 
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd rm argocd-linux-amd64

```
## Accessing the ArgoCD interface
We will use port forwarding to access the ArgoCD dashboard, run the following command
```bash
kubectl port-forward svc/argocd-server -n kal-argocd 8080:443
```
open your browser at localhost:8080 then you should see the ArgoCD UI. 
## Login to ArgoCD
To login to the ArgoCD interface, we need to get the initial password for ArgoCD
```bash
 kubectl -n kal-argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
login to the UI using the username `admin` and the password you get from the previous  step.

Once you logged in then add the current context kubernetes cluster to the argocd using the following command
```bash
# Provide the username and the password when requested
argocd login localhost:8080
```
Adding the cluster to the argo
```bash
argocd cluster add [context-name] --name [friendly name]
```
Answer all the questions correctly and go back to your argocd UI and navigate to settings >> clusters section and see if the newly added cluster have the status of `successful`

## Configure Ingress for ArgoCD
To access your ArgoCD UI from remote environment you should configure ingress for it. To do that please create the following ingress file called `argocd-ingress.yaml` and add the following content.
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: argocd.kal.gebeyalearning.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              number: 443
```
Next apply the above ingress on your argocd namespace
```bash
kubectl apply -f argocd-ingress.yaml -n kal-argocd
```
Now you can access ArgoCD from remote environment without having access to the Kubernetes cluster.
