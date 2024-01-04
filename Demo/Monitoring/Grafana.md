# Install and use Grarfana
## What is Grafana
- Grafana is a multi-platform Open-source analytics and interactive visualization web application.
- It provides charts, graphs and alerts for the web when connected to supported data services.
- Grafana allows us to query, visualize, alert on and understand our metrics, no matter where they are stored. Some supported data sources in addition to Prometheus are AWS CloudWatch, AzureMonitor, PostgreSQL, Elasticsearch and many more.
- We can create our own dashboards or use the existing ones provided by Grafana. We can personalize the dashboards as per our requirements.
## Installing Grafana
To install Grafana on our cluster we will use helm chart. please refer [this](https://grafana.github.io/helm-charts/) or [here](https://github.com/grafana/helm-charts/tree/main/charts/grafana) for the chart custom attributes for more.
```bash
helm repo add grafana https://grafana.github.io/helm-charts --namepsace monitoring --create-namespace

helm install grafana grafana/grafana -n monitoring --set ingress.enabled=true --set ingress.hosts=["grafana.gebeyalearning.com"] --set ingress.ingressClassName=nginx
```

Check all the services by using the following command.

```bash
kubectl get po -n monitoring
```

Now open your browser pointing to `grafana.gebeyalearning.com` and login with the following credentials.
To get the credentials run the following command
```bash
kubectl get secret --namespace default grafana -o jsonpath='{.data.password_value}' | base64 -d; echo
kubectl get secret --namespace default grafana -o jsonpath='{.data.username_value}' | base64 -d; echo
```