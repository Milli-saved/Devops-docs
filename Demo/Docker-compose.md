# Docker Compose Demo with Node.js and MongoDB

In this demonstration, we will use `docker-compose` to orchestrate a multi-container environment with a Node.js application and a MongoDB database.
Before you run the following demo make sure you install docker-compose on the machine [Installing Docker](https://git.gebeya.training/devops-docs/devops-course-technical-docs/-/blob/main/Docker%20&%20docker-compose/Installing%20Docker.md?ref_type=heads)

## Step 1: Create Docker Compose YAML File

Create a file named `docker-compose.yml` in your project directory and add the following content:

```yaml
version: '3'
services:
  web:
    image: node:14
    ports:
      - "8080:8080"
    volumes:
      - ./app:/usr/src/app
    working_dir: /usr/src/app
    command: npm start
    depends_on:
      - mongo
  mongo:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - ./data:/data/db
```
This `docker-compose.yml` defines two services: `web` for the Node.js application and `mongo` for the MongoDB database. It specifies port mappings, volume mounts, and dependencies.

## Step 2: Create Node.js Application

Create a simple Node.js application in a folder named `app` within your project directory. Ensure the application has a `package.json` file with a `start` script. or you can start from one of the demo projects listed at [[Pipeline demo projects]]

## Step 3: Build and Run the Docker Compose Environment


```bash
docker-compose up
```

This command reads the `docker-compose.yml` file, pulls the necessary images, and starts the defined services.

## Step 4: Access Node.js Application

Visit `http://localhost:8080` in your web browser to access the Node.js application. Verify that it's connected to the MongoDB database.

## Step 5: Inspect Running Containers


```bash
docker-compose ps
```

List the status of the containers managed by Docker Compose.

## Step 6: Stop and Remove Containers

```bash
docker-compose down
```

Stop and remove the running containers. This also removes the associated volumes.

## Additional Configuration (Optional)

You can customize the `docker-compose.yml` file to add more services, configure networks, or set environment variables.

```yaml
services:
  ...
  additional_service:
    image: custom_image:tag
    ports:
      - "8081:8081"
    environment:
      - VARIABLE_NAME=value

```

Feel free to explore more advanced features and options in the [official Docker Compose documentation](https://docs.docker.com/compose/).