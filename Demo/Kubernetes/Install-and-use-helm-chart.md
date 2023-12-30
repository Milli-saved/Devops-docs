# Installation of Helm Chart
You can install helm chart to your local machine by referencing their [documentation](https://helm.sh/docs/intro/install/) page. as I am Using WSL for my local environment I used the scripting installation.
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
The above script will install helm CLI on your local machine.
To check the correct installation of the helm cli run the following command
```bash
helm version
```

# Create your first nginx helm chart
To create helm chart deployment for nginx please run the following command.
```bash
helm create my-nginx-app
```
The above command will create the folder structure similar to the following.
```bash
my-nginx-app
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```
## Deploying using your first helm chart
Before you do your deployment make sure your `values.yaml` file contains the correct values. For instance if you want to update the image name update the following section in the values.yaml file.
```yaml
image:
  repository: nginx
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""
```
Then now run the following command to do the deployment
```bash
# helm install [release-name] [repository-name] [options]
helm install my-nginx ./my-nginx-app/ --namespace kal-test --create-namespace
```
To check if the deployment succeeded run the following command
```bash
helm list -n kal-test
```
You will see an output similar to the following.
```bash
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
my-nginx        kal-test        1               2023-12-30 08:47:51.551340759 +0300 EAT deployed        my-nginx-app-0.1.0      1.16.0
```

## Deployment of Nginx Ingress Controller using Helm chart.
Nginx ingress controller is a service that will be deployed and facilitate the correct routing of ingresses in the Kubernetes by using single Loa balancer. Here is the installation [documentation](https://kubernetes.github.io/ingress-nginx/deploy/)
Run the following command to install the ingress controller
```bash
helm upgrade --install ingress-nginx ingress-nginx \ --repo https://kubernetes.github.io/ingress-nginx \ --namespace ingress-nginx --create-namespace
```
It will create namespace called `ingress-nginx` to check if it succeeded the installtion run the command
```bash
helm list -n ingress-nginx
```
You should get an output similar to the following
```bash
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
ingress-nginx   ingress-nginx   1               2023-12-30 08:55:59.626185321 +0300 EAT deployed        ingress-nginx-4.9.0     1.9.5
```

To see the load balancer IP run the following command.
```bash
kubectl get svc -n ingress-nginx
```
You will get an output as follows
```bash
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.0.243.38   20.93.241.212   80:32356/TCP,443:31825/TCP   2m9s
ingress-nginx-controller-admission   ClusterIP      10.0.253.50   <none>          443/TCP                      2m9s
```
Please take a note of the External IP from service type  `LoadBalancer`
The next step is to attach your LB IP to your domain registral and map the IP with your preferred domain.

## Creating ingress to see routing
Create file called `my-first-ingress.yaml` and add the following content
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-nginx-app-ingress
spec:
  rules:
  - host: my-domain-name
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-nginx-my-nginx-app
            port:
              number: 80
```
Then apply the above file in your namespace
```bash
kubectl apply -f my-first-ingress.yaml -n kal-test
```
Now open your browser and access your website by using the value stated in the `host` above

## Uninstalling helm charts
To uninstall your chart run the following command
```bash
# helm unistall [releasename] -n namespace
helm uninstall my-nginx -n kal-test
```
It will remove the helm chart.