# Kubernetes Exam
## Objective
In this examination, we will assess your comprehension of Kubernetes and its integral components. Furthermore, we aim to validate your capability to proficiently interact with and deploy applications within a Kubernetes cluster.
## Allowed Time
You have a total of 2 hours for this exam. Manage your time efficiently to complete all tasks within the allocated timeframe.
## Total points for the exam
The total points for the exam are capped at 10. Each instruction carries a specific point value, and adherence to each instruction is crucial for scoring. Failure to correctly follow an instruction will result in a deduction of points. Ensure meticulous execution to maximize your score.
## Deployment Architecture
Refer to the accompanying diagram below for a visual representation of the deployment architecture. This diagram is designed to enhance your comprehension and provide a clear understanding of the expected outcome resulting from the deployment.
![[Pasted image 20231230080757.png]]
## Instruction
1. **Create a Secret for PostgreSQL Database Password:**
    - Generate a secret named `postgres-password-secret` containing a single key called `postgres-password`. Feel free to set the value to your preference.
2. **Provision PostgreSQL Database using StatefulSet:**
    - Develop a deployment manifest utilizing the `StatefulSet` kind to deploy a PostgreSQL database named `postgres-db-sfs`. Incorporate an environment variable in the deployment manifest to reference the database password from the previously created secret. Name the environment variable `POSTGRES_PASSWORD`, and set the container port to `5432`. The data volume for the postgres container is  at `/var/lib/postgresql/data
3. **Establish a Service for PostgreSQL Database:**
- Create a service for the PostgreSQL database named `postgres-db-svc`.
4. **Interact with the PostgreSQL Database Pod:**
- Access the PostgreSQL pod and connect to the PostgreSQL database CLI using the following command: `psql -U postgres -W` (press Enter and provide your password when prompted).
- Within the PostgreSQL CLI, create a database using the command: `CREATE DATABASE todo-app;`. Exit the pod after executing the command.
5. Create the deployment named `backend-api-dep` and the service named `backend-api-svc` for the backend application, adhering to the provided specifications:

	1. **Define Environment Variables:**
	    - Set up the following environment variables within the deployment manifest:
	        - `POSTGRES_SERVER`: Set to `postgres-db-svc`.
	        - `POSTGRES_USER`: Set to `postgres`.
	        - `POSTGRES_PASSWORD`: Retrieve this value from the PostgreSQL secret created in step 1.
	        - `POSTGRES_DB`: Set to `todo-app`.
	        - `BACKEND_CORS_ORIGINS`: Set to `["fe.[your-domain-name]"]`.
	        - `PROJECT_NAME`: Set to `My-Todo-app`.
	2. **Specify Container Image:**
	    - Utilize the container image `kaleabg/saf-ja-k8s-test-backend:1.0.0`.
	3. **Configure Container Port:**
	    - Set the container port to `80`.
6. Create ingress with the following configuration, make sure you update the domain name with the right domain name we provided to you.
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: backend-ingress
spec:
  rules:
  - host: be.[your-domain-name]
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: backend-api-svc
            port:
              number: 80

```
7. Create a deployment named `frontend-dep` and a service named `frontend-svc` for your frontend application, incorporating the following details:

	1. **Configure Environment Variables:**
	    - Within the deployment manifest, set the following environment variable:
	        - `REACT_APP_BACKEND_BASE_URI`: Specify as `"http://be.[your-domain-name]/api/items"`. do not add trailing  slash at the end.
	2. **Specify Container Image:**
	    - Utilize the container image `kaleabg/saf-ja-k8s-test-frontend:1.0.0`.
	3. **Define Container Port:**
	    - Ensure the container port is configured as `80`.
8. Create ingress with the following configuration, make sure you update the domain name with the right domain name we provided to you.
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: fe.[your-domain-name]
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-svc
            port:
              number: 80

```
## Accessing the application
Now try to access the frontend application using the domain name fe.[your-domain-name]. you should get the following output. If not please double check your configurations
![[Pasted image 20231229170231.png]]

## Solution
Exam Solution can be found in the folder ` k8s-exam-solution` 
