# Using Secrets
There are different types of secrets, for example the secret used for docker authentication is type `kubernetes.io/dockerconfigjson` for general purpose use `opaque`
## Creating secrets
To create secret on your Kubernetes cluster use the following template. Create file called `my-mongo-secret.yaml` and add the following content

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW5kYXRhYmFzZQ==  # base64-encoded username
  password: YWRtaW5wYXNzd29yZA==  # base64-encoded password

```
The value above we used for username and password is base64 encoded, here are the linux command to encrypt and decrypt
```bash
# Encodeing
echo -n "your-user-name-here" | base64
# Decodding
echo -n "your-base64-encoded-string" | base64 -d
```
Else you can use online tools to encode and decode your string [here](https://www.base64encode.org/)

## Using secret
To use the above created secret lets update our MongoDB deployment we used in our previous sessions as follows
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-mongodb
spec:
  serviceName: "mongodb"
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
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
  volumeClaimTemplates:
  - metadata:
      name: mongodb-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "azurefile"
      resources:
        requests:
          storage: 1Gi

```

We used statefulSet deployment type for the as MongoDB is stateful service. The above manifest creates PVC and PV as well as it uses the secret to get the initial username and the password for the MongoDB.

# Class Activity
Apply the skill of using secret on our previous CMS application 
1. Use the above MongoDB to connect with your previous CMS application
2. Update the config/database.js file of the CMS project to add username and password environment variable on the database connection string as follows
```js
 mongoDbUrl: `mongodb://${process.env.DATABASE_USERNAME}:${process.env.DATABASE_PASSWORD}@${process.env.DATABASE_HOST}/${process.env.DATABASE_NAME}?retryWrites=true&w=majority`
```
3. Update the deployment file for the CMS app to use the secret we created above `my-secret` and set the Environment variables for the Username and Password from the secret
4. Apply your changes on your namespace and see if the application works.

# Annex
## Different types of secrets
### Standard Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: dXNlcm5hbWU=  # base64-encoded username
  password: cGFzc3dvcmQ=  # base64-encoded password
```
Used to encrypt any kind of secrets with KeyValue pair
### Docker Registry Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-docker-secret
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <base64-encoded-docker-config-json>

```
Used of authentication of private or secured docker registries
To generate `.dockerconfigjson`, you can use: 
```bash
echo -n '{"auths":{"<registry-url>":{"username":"<username>","password":"<password>","email":"<email>","auth":"'"$(echo -n '<username>:<password>' | base64 -w0)"'"}}}' | base64 -w0
```
### TLS Certificate Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-tls-secret
type: kubernetes.io/tls
data:
  tls.crt: <base64-encoded-certificate>
  tls.key: <base64-encoded-private-key>

```
Used for TLS certificates
