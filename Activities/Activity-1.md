# Activity 1
Project repository: https://git.gebeya.training/todo-microservice
## Objective
Implement a complete CI/CD pipeline to build and deploy the microservices on the above project link
## Architecture
```mermaid
graph TD
  subgraph Frontend
    frontend -->|Rest API| api-service
  end

  subgraph Backend Service
    api-service -->|grpc| auth-service
    api-service -->|grpc| todo-service
  end

  subgraph Auth Service
    auth-service -->|Database| PostgresDB
  end

  subgraph Todo Service
    todo-service -->|Database| MongoDB
  end
```
## Tasks
1. Fork the above repo
2. Configure CI/CD pipeline to do docker build push and update your deployment manifests with the correct image tag
3. Create deployment manifest project
4. Create deployment (you can use either statefulset or deployment) manifest for PostgreSQL and MongoDB with PV and PVC make sure the PVC can be mountable
5. Create deployment manifest for the microservices including the frontend
6. Create ingress for the API service and frontend service
7. Create application on your ArgoCD and make sure all the apps are deployed and running.

## Due date 
1 week