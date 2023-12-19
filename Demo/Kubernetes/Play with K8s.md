# Play with K8s cluster
In this demo we assume that you already installed kind and kubectl as well as created your own cluster.
The following commands will be executed in existing cluster

## Cluster Management

Display endpoint information about the master and services in the cluster

```json
kubectl cluster-info
```

Display the Kubernetes version running on the client and server

```json
kubectl version
```

Get the configuration of the cluster

```json
kubectl config view
```

List the API resources that are available

```json
kubectl api-resources
```

List the API versions that are available

```json
kubectl api-versions
```

List everything

```json
kubectl get all --all-namespaces
```

## Nodes
Get list of available Nodes
```json
kubectl get nodes
```

Get details of node
```json 
kubectl describe nodes
```
## Namespaces
**Shortcode = ns**

List one or more namespaces

```json
kubectl get namespace <namespace_name>
```

Display the detailed state of one or more namespace

```json
kubectl describe namespace <namespace_name>
```
Create namespace [name]

```json
kubectl create namespace <namespace_name>
```
Edit and update the definition of a namespace

```json
kubectl edit namespace <namespace_name>
```

Delete a namespace
```json
kubectl delete namespace <namespace_name>
```

## Deployments

**Shortcode = deploy**

List one or more deployments

```json
kubectl get deployment
```

Get deployment from kube-system namepsace
```json
kubectl get deploy --namespace kube-system
```
Create one a new deployment

```json
kubectl create deployment <deployment_name>
```

Display the detailed state of one or more deployments

```json
kubectl describe deployment <deployment_name>
```

Edit and update the definition of one or more deployment on the server

```json
kubectl edit deployment <deployment_name>
```

See the rollout status of a deployment

```json
kubectl rollout status deployment <deployment_name>
```

Delete deployments

```json
kubectl delete deployment <deployment_name>
```

## Pods

**Shortcode = po**

List one or more pods

```json
kubectl get pod
```

Display the detailed state of a pods

```json
kubectl describe pod <pod_name>
```

Execute a command against a container in a pod

```json
kubectl exec <pod_name> -c <container_name> <command>
```

Get interactive shell on a a single-container pod

```json
kubectl exec -it <pod_name> /bin/sh
```

Add or update the label of a pod

```json
kubectl label pod <pod_name>
```
Delete a pod

```json
kubectl delete pod <pod_name>
```

## Logs

Print the logs for a pod

```json
kubectl logs <pod_name>
```

Print the logs for the last hour for a pod

```json
kubectl logs --since=1h <pod_name>
```

Get the most recent 20 lines of logs

```json
kubectl logs --tail=20 <pod_name>
```

Get logs from a service and optionally select which container

```json
kubectl logs -f <service_name> [-c <$container>]
```

Print the logs for a pod and follow new logs

```json
kubectl logs -f <pod_name>
```

Print the logs for a container in a pod

```json
kubectl logs -c <container_name> <pod_name>
```

Output the logs for a pod into a file named ‘pod.log’

```json
kubectl logs <pod_name> pod.log
```

View the logs for a previously failed pod

```json
kubectl logs --previous <pod_name>
```
## Events

**Shortcode = ev**

List recent events for all resources in the system

```json
kubectl get events
```

List Warnings only

```json
kubectl get events --field-selector type=Warning
```

List events but exclude Pod events

```json
kubectl get events --field-selector involvedObject.kind!=Pod
```

Pull events for a single node with a specific name

```json
kubectl get events --field-selector involvedObject.kind=Node, involvedObject.name=<node_name>
```

Filter out normal events from a list of events

```json
kubectl get events --field-selector type!=Normal
```

## Secrets

Create a secret

```json
kubectl create secret
```

List secrets

```json
kubectl get secrets
```

List details about secrets

```json
kubectl describe secrets
```

Delete a secret

```json
kubectl delete secret <secret_name>
```

## Services

**Shortcode = svc**

List one or more services

```json
kubectl get services
```

Display the detailed state of a service

```json
kubectl describe services
```

Expose a replication controller, service, deployment or pod as a new Kubernetes service

```json
kubectl expose deployment [deployment_name]
```

Edit and update the definition of one or more services

```json
kubectl edit services
```

# Kubernetes resources with Manifests file
## Sample deployment 
Lets create deployment file called `my-nginx-deploy.yaml` to do nginx deployment in the directory you want and add the following details
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
Create service file called `my-nginx-svc.yml` and add the following content
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  labels:
    app: nginx
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
```

Now apply your file to namespace you want by using the following command
```bash
kubectl apply -f my-nginx-deploy.yml --namespace [yournamespace]
kubectl apply -f my-nginx-svc.yml --namespace [yournamespace]
```
Verify the resources
```bash
kubectl get deployments -n [yournamespace]
kubectl get services -n [yournamespace]
```
Verify the pod log
```bash
kubectl logs -f po -n [yournamespace] [pod name]
```
port forward your application to see the deployed application using the following command 
```bash
kubectl port-forward --address 0.0.0.0 svc/nginx-svc 8080:80 -n [yournamespace]
# Open your browser and connect to the http://172.205.128.194:8080
```
Delete the resource
```bash
kubectl delete -f my-nginx.yml --namespace [yournamespace]
```

## Demo 
The following project will fully demo the deployment process
1. Clone the following git project https://github.com/learning-zone/website-templates/tree/master
2. Select the template you want and create Docker file for it
3. Build the docker image and push it to the docker hub
4. Create deployment template file based on the above sample by setting the image source to your docker image
5. Create service yaml and attach it to the deployment 
6. port forward to the port you have given last time and access it from your browser.