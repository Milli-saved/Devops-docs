# Using Cloud AKS Cluster
In this training the Kubernetes cluster we will use is hosted on azure and we need to first authenticate ourselves to Azure by using the following stages.
## Stage 1: Install AZ cli
To install azure cli please go to their [documentation]([How to install the Azure CLI | Microsoft Learn](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)) page and follow the instruction based on your OS.
## Stage 2: Login using the cli
Open your preferred command line interface with az cli installed and run the following command.
```sh
az login
```
The next stage is to add the Kubernetes cluster to your local kubectl configs by running the command
```sh
# az aks get-credentials --resource-group <YourResourceGroupName> --name <YourAKSClusterName>
az aks get-credentials --resource-group CICD-Deployment-RG-Test --name Gg-devops-training-49a717a177e02381
```
No you are connected to the Kubernetes cluster, to verify that you are connected run the following command to get the list of nodes, or Kubernetes cluster info

```json
kubectl cluster-info
kubectl get nodes
```

# Using Persistent volumes on K8s
create the file called `my-pv.yaml` with the following content
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azure-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  azureFile:
    secretName: azure-secret
    shareName: myshare
    readOnly: false
  claimRef:
    kind: PersistentVolumeClaim
    name: azure-pvc
  persistentVolumeReclaimPolicy: Delete
  storageClassName: default
  volumeMode: Filesystem

```
and create the following file to create you persistent volume claim named `my-pvc.yaml`
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```
now create new namespace and apply your files using the following command
```sh
kubectl apply -f "my-pv.yaml,my-pvc.yaml" -n test
```
check your deployment if it went successful.
```sh
kubectl get pv -n test
kubectl get pvc -n test
```
## Use the deployed PVC in your deployment
To use the deployed PVC you should use volumeMount and Volumes section in your deployment template.
use the following template. create deployment manifest with name `my-deploy-with-pvc.yaml` and add the following content

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
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
        volumeMounts:
        - name: nginx-persistent-storage
          mountPath: /usr/share/nginx/html
      volumes:
      - name: nginx-persistent-storage
        persistentVolumeClaim:
          claimName: azure-pvc

```
now apply this manifets to your namespace
```sh
kubectl apply -f my-deploy-with-pvc.yaml -n test
```
now lets exec to the container and update the files in the `/usr/share/nginx/html` folder.
```sh
 kubectl exec -it nginx-deployment-5fbf497c58-lpv65 -n test -- /bin/sh
```
Then run the following command to create index.html file in the path
```bash
cat <<EOF > index.html
<!DOCTYPE html>
<html>
<head>
  <title>My First PVC nginx</title>
</head>
<body>

  <h1>Hello, World!</h1>
  <p>This is My first nginx with PVC in k8s.</p>

</body>
</html>
EOF

```
now to check if my changes are applied to the pod run the following port forwarding command
```bash
kubectl port-forward --address 0.0.0.0 po/nginx-deployment-5fbf497c58-lpv65 8080:80 -n test
```
Then try to access the page using `localhost:8080` from your browser.
## Recreate the deployment 
Lets firsts delete the deployment and recreate it to see if our changes to the container in the previous stage persists.
```bash
kubectl delete -f my-deploy-with-pvc.yaml -n test
kubectl get po -n test
# Should not return any pod

# Recreate the pod using the following command and port forward it
kubectl apply -f my-deploy-with-pvc.yaml -n test
kubectl port-forward --address 0.0.0.0 po/nginx-deployment-5fbf497c58-l4j7f 8080:80 -n test
```
