# Install and configure Prometheus
## What is Prometheus 
- Prometheus is an Open-source systems monitoring and alerting toolkit.
- Prometheus collects and stores the metrics as time series data.
- It provides out-of-box monitoring capabilities for container orchestration platforms such as Kubernetes.
## Installing Prometheus
We will use helm chart to install Prometheus service. use the following command to install it on a namespace called `monitoring` You can read the more from [this](https://prometheus-community.github.io/helm-charts/) source
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm install prometheus prometheus-community/prometheus -n monitoring --create-namespace
```
Now get the status of the pods by running the following command
```bash
kubectl get po -n monitoring
```
