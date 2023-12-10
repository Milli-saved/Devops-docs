# Docker Demo with Nginx

In this demonstration, we will use the Nginx Docker image to showcase various Docker commands, including network, exec, volume, and port mapping.
Before you run the following demo make sure you install docker on the machine [Installing Docker](https://git.gebeya.training/devops-docs/devops-course-technical-docs/-/blob/main/Docker%20&%20docker-compose/Installing%20Docker.md?ref_type=heads)

## Step 1: Pull Nginx Image

```bash
docker pull nginx
```
This command downloads the official Nginx image from the Docker Hub.

## Step 2: Run Nginx Container

```bash
docker run -d --name mynginx -p 8080:80 nginx
```
This command starts a new Nginx container in detached mode, names it `mynginx`, and maps port 8080 on the host to port 80 in the container.
## Step 3: List Running Containers

```bash
docker ps
```

Check the list of running containers to verify that the Nginx container is running.

## Step 4: Access Nginx in the Browser

Visit `http://localhost:8080` in your web browser to see the default Nginx welcome page.

## Step 5: Execute Command Inside Container

```bash
docker exec -it mynginx bash
```

This command opens a bash shell inside the running Nginx container. You can now execute commands within the container.

## Step 6: Create and Mount a Volume


```bash
docker volume create nginx_volume docker run -d --name nginx_with_volume -p 8081:80 -v nginx_volume:/usr/share/nginx/html nginx
```

This creates a Docker volume named `nginx_volume`, runs a new Nginx container with the volume mounted, and maps port 8081 on the host to port 80 in the container.

## Step 7: List Docker Volumes

```bash
docker volume ls
```

Check the list of Docker volumes to verify that `nginx_volume` has been created.

## Step 8: Inspect Docker Volume

```bash
docker volume inspect nginx_volume
```

View detailed information about the `nginx_volume` Docker volume.

## Step 9: Network Inspection

```
docker network inspect bridge
```

Inspect the default Docker bridge network to view details, including connected containers.

## Step 10: Stop and Remove Containers

```bash
docker stop mynginx nginx_with_volume docker rm mynginx nginx_with_volume
```

Stop and remove the Nginx containers.

## Step 11: Remove Docker Volume

```bash
docker volume rm nginx_volume
```

Remove the `nginx_volume` Docker volume.

These steps provide a basic demonstration of working with Docker containers using the Nginx image and various Docker commands. Feel free to explore additional Docker features and options as needed at the docker official documentation page.
