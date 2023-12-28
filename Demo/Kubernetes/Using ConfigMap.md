# Using ConfigMap
ConfigMaps are used to override some application configurations at runtime, let do the example with nginx.
## Create configMap
To create configmap create file called `my-configmap.yaml` and add the following content
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  app.conf: |
    server {
        listen 8080;
        server_name my-app.com;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
    }
```
Apply the configmap manifest file to your namespace
```bash
kubectl apply -f my-configmap.yaml -n [your-namespace]
```
## Using the configmp on deployment
Create deployment manifest called `my-nginx.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: nginx-config-volume
          mountPath: /etc/nginx/conf.d
      volumes:
      - name: nginx-config-volume
        configMap:
          name: nginx-config

```
Apply the deployment 
```bash
kubectl apply -f my-nginx.yaml -n [namepsace]
```
Now port forward to the nginx config and see if it can connect to port 8080.
```bash
kubectl port-forward --address 0.0.0.0 po/nginx-deployment-5fbf497c58-l4j7f 8080:8080 -n kal-test
```

# Class Activity
1. Create configmap with key as `database.js` and value as follows
```js
//This is my config mounted from the configmap
module.exports = {
    mongoDbUrl: `mongodb://${process.env.DATABASE_USERNAME}:${process.env.DATABASE_PASSWORD}@${process.env.DATABASE_HOST}/${process.env.DATABASE_NAME}?retryWrites=true&w=majority`
};
```
2. Update the CMS application deployment manifest to mount volume at `/my-app/config` folder 
3. Apply the deployment manifest
4. Connect to the pod using ``kubectl exec -it . . .`` and see if it contains your changes in the database.js file it should look like the above.