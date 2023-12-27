# k8s - Solution for Multi pod deployment
## clone the repo
```sh
git clone https://github.com/nafisatuli/CMS-system.git
cd CMS-system
```
## Update the database connection 
Open the `config/database.js` file from the cloned repo and change the line 2 by the following value
```js
 mongoDbUrl: `mongodb://${process.env.DATABASE_HOST}/${process.env.DATABASE_NAME}?retryWrites=true&w=majority`
```
## Create Dockerfile for the project
Add the file called `Dockerfile` in the root directory of the project and add the following content
```Dockerfile
FROM node:18.0.0
WORKDIR /my-app
COPY package*.json ./
RUN npm install 
COPY . ./
EXPOSE 4500
ENTRYPOINT ["npm", "start"]
```
## Build the docker image 
In my case I'll use the docker build stage with my docker hub registry as follows
```bash
docker build -t kaleabg/my-devops-cms:1.0.1 .
docker push kaleabg/my-devops-cms:1.0.1
```
## Create Mongodb Deployment manifest with PV and PVC
Lest first create pv with the file name `monogdb-pv.yaml`
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mongodb-pv
spec:
  capacity:
    storage: 8Gi
  accessModes:
    - ReadWriteOnce
  azureFile:
    secretName: azure-secret
    shareName: myshare
    readOnly: false
  claimRef:
    kind: PersistentVolumeClaim
    name: mongodb-pvc
  persistentVolumeReclaimPolicy: Delete
  storageClassName: default
  volumeMode: Filesystem

```
Create pvc file with the name `monogdb-pvc.yaml`
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
```
Create deployment manifest for the mongodb with name `mongodb-deploy.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:latest
        ports:
        - containerPort: 27017
        volumeMounts:
        - name: mongodb-storage
          mountPath: /data/db
  volumes:
  - name: mongodb-storage
    persistentVolumeClaim:
      claimName: mongodb-pvc
```
Create mongodb Service file with name `mongodb-service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
  type: ClusterIP
```

Apply the deployment manifest to the Kubenrnetes cluster
```bash
kubeclt apply -f "mongodb-pv.yaml,mongodb-pvc.yaml,mongodb-deploy.yaml,mongodb-service.yaml" -n [my-namespace]
kubectl get po -n [my-namespace]
```

## Create deployment manifest for the Application

Lest first create pv with the file name `cms-pv.yaml`
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: cms-pv
spec:
  capacity:
    storage: 8Gi
  accessModes:
    - ReadWriteOnce
  azureFile:
    secretName: azure-secret
    shareName: myshare
    readOnly: false
  claimRef:
    kind: PersistentVolumeClaim
    name: cms-pvc
  persistentVolumeReclaimPolicy: Delete
  storageClassName: default
  volumeMode: Filesystem

```
Create pvc file with the name `cms-pvc.yaml`
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: cms-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
```
Create deployment manifest for the mongodb with name `cms-deploy.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cms-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cms-app
  template:
    metadata:
      labels:
        app: cms-app
    spec:
      containers:
      - name: cms-app
        image: kaleabg/my-devops-cms:1.0.1
        ports:
        - containerPort: 4500
        env: 
        - name: DATABASE_HOST 
          value: "mongodb-service"
        - name: DATABASE_NAME 
          value: "my-cms-app"
        volumeMounts:
        - name: cms-app-storage
          mountPath: /my-app/public/uploads
  volumes:
  - name: cms-app-storage
    persistentVolumeClaim:
      claimName: cms-pvc
```
Create mongodb Service file with name `cms-service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: cms-app-service
spec:
  selector:
    app: cms-app
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 4500
  type: ClusterIP
```

Apply the deployment manifest to the Kubenrnetes cluster
```bash
kubeclt apply -f "cms-pv.yaml,cms-pvc.yaml,cms-deploy.yaml,cms-service.yaml" -n [my-namespace]
kubectl get po -n [my-namespace]
```

