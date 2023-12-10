# Basic Docker Commands

Docker commands are used to interact with containers and manage Docker images. Here are some fundamental Docker commands to get you started:
Before you run the following commands make sure you install docker on the machine [Installing Docker](https://git.gebeya.training/devops-docs/devops-course-technical-docs/-/blob/main/Docker%20&%20docker-compose/Installing%20Docker.md?ref_type=heads)

## 1. Image Commands

### List Docker Images

```bash
docker images
```
This command displays a list of all Docker images on your system.

### Pull an Image
```bash
docker pull [image_name]
```
Use this command to download a Docker image from a registry.

## 2. Container Commands

### Run a Container
```bash
docker run [options] [image_name]
```
This command creates and starts a new container based on the specified image.

### List Running Containers
```bash
docker ps
```
Shows a list of running containers.

### List All Containers
```bash
docker ps -a
```
Displays all containers, including stopped ones.

### Stop a Running Container
```bash
docker stop [container_id or container_name]
```
Stops a running container.

### Remove a Container
```bash
docker rm [container_id or container_name]
```
Deletes a stopped container.

### Remove All Containers
```bash
docker rm $(docker ps -a -q)
```
Deletes all stopped containers.

## 3. Executing Commands in a Running Container

### Execute a Command in a Running Container
```bash
docker exec -it [container_id or container_name] [command]
```
Runs a command inside a running container.

## 4. Network Commands

### List Docker Networks
```bash
docker network ls
```
Displays a list of Docker networks.

### Inspect a Network
```bash
docker network inspect [network_name]
```
Shows detailed information about a Docker network.

## 5. Volume Commands

### List Docker Volumes
```bash
docker volume ls
```
Lists all Docker volumes.

### Inspect a Volume
```bash
docker volume inspect [volume_name]
```
Displays detailed information about a Docker volume.

These are just a few basic Docker commands. Explore the [official Docker documentation](https://docs.docker.com/engine/reference/commandline/cli/) for more advanced options and features.