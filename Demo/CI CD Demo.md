## Demo Project Import

1. Import the demo project into your GitLab repository by using the following link:
    - [Node Express ES8 Demo](https://github.com/shekhar-raval/node-express-es8)

## Adding CI/CD Pipeline Configuration

1. In the root directory of the project, add the GitLab CI/CD pipeline configuration file named `.gitlab-ci.yml`.
2. Insert the following content into the configuration file:
```yml
stages:
  - test
  - build
  - deploy

# -----------------------------------------CI Stage Starts here ----------------------
node_test:
  image: node:18.0.0
  stage: test
  before_script:
    - npm install
  script:
    - npm run test-ci

node_coverage:
  image: node:18.0.0
  stage: test
  before_script:
    - npm install
  script:
    - npm run test-coverage

docker_build:
  image: docker:24.0.7
  stage: build
  variables:
    DOCKER_IMAGE: $DOCKER_USER_NAME/$CI_PROJECT_NAME:1.0.2
  before_script:
    - docker login -u $DOCKER_USER_NAME -p $DOCKER_PASSWORD
  script:
    - echo $DOCKER_PASSWORD
    - docker build -t $DOCKER_IMAGE .
    - docker push $DOCKER_IMAGE
# ----------------------------------------------CI Stage Ends Here----------------------

# -----------------------------------------CD Stage Starts here ----------------------
deploy_to_ec2:
  stage: deploy
  variables:
   DOCKER_IMAGE: $DOCKER_USER_NAME/$CI_PROJECT_NAME:$CI_COMMIT_SHA
   CONTAINER_NAME: "kaleab-${CI_PROJECT_NAME}"
   APP_PORT: 8000
  before_script:
    - apk add openssh-client
    - eval $(ssh-agent -s)
    - echo "$CD_SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
  script:
    - ssh -o StrictHostKeyChecking=no $DEPLOYMENT_HOST "docker kill $CONTAINER_NAME || true && docker rm  $CONTAINER_NAME || true && docker run -dp 8080:$APP_PORT --name $CONTAINER_NAME -e PORT=$APP_PORT -e RESTREVIEWS_DB_URI="mongodb://my-mongo:27017" --network $APP_NETWORK $DOCKER_IMAGE"
# ----------------------------------------------CD Stage Ends Here----------------------

```
## Adding Dockerfile

1. To enable the pipeline to build a Docker image, add a `Dockerfile` to the root directory of the project.
2. Insert the following content into the `Dockerfile`:
```Dockerfile
FROM node:18.0.0
WORKDIR /my-app
COPY package*.json ./
RUN npm install 
COPY . ./
EXPOSE 8080
ENTRYPOINT ["npm", "start"]
```
## Commit Your Project

1. Add the new files to the repository and push them to the remote repository:
```bash
git add .
git commit -m "fix: adding pipeline config and Dockerfile"
git push
```
**Note:** Ensure you have added the following variables in the GitLab CI/CD settings page for the pipeline to work correctly:

- CD_SSH_PRIVATE_KEY
- APP_NETWORK
- DOCKER_USER_NAME
- DOCKER_PASSWORD
- DEPLOYMENT_HOST

## Configuring the Remote Server for SSH

For the CD stage to function correctly, make sure you perform the SSH key addition. Follow the steps in [[Adding SSH Public Key to the Remote Server]].