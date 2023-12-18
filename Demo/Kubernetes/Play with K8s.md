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
