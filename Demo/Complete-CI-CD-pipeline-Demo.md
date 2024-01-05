# Complete CI/CD pipeline demo
Please fork the following group backend and frontend projects
https://git.gebeya.training/jica-safaricom-devops/first-todo-app

Then create GitLab CI pipeline to do the following
- Docker build
- Docker push
- Update image tag to the deployment manifest repo
## Creating CI pipeline
Create .gitlab-ci.yml file in the root directory of the project with the following content. Please make sure you update all the values attached with me to yours for example the value for the var `MANIFEST_GIT_REPO`.
backend
```yaml
stages:
  - build
  - update-manifest
docker_build:
  image: docker:24.0.7
  stage: build
  variables:
    APP_NAME: "first-todo-app-"
    DOCKER_IMAGE: $DOCKER_USER_NAME/${$APP_NAME}${CI_PROJECT_NAME}:$CI_COMMIT_SHA
  before_script:
    - docker login -u $DOCKER_USER_NAME -p $DOCKER_PASSWORD
  script:
    - echo $DOCKER_PASSWORD
    - docker build -t $DOCKER_IMAGE -f backend.dockerfile .
    - docker push $DOCKER_IMAGE
  
update-manifest:
    stage: update-manifest
    variables:
        APP_NAME: "first-todo-app-"
        DOCKER_IMAGE: $DOCKER_USER_NAME/${$APP_NAME}${CI_PROJECT_NAME}:$CI_COMMIT_SHA
        MANIFEST_GIT_REPO: https://$USERNAME:$PAT_TOKEN@git.gebeya.training/devops-ci-cd/argocd-first-test.git
    before_script:
        - apk add git
        - git clone $MANIFEST_GIT_REPO
        - git config --global user.name "$GITLAB_USER_NAME"
        - git config --global user.email "$GITLAB_USER_EMAIL"
    script:
        - cd argocd-first-test
        - git remote set-url origin --push $MANIFEST_GIT_REPO
        - |
            sed -i "s/image:.*/image: $DOCKER_IMAGE/g" ./backend/be-deployment.yaml
        - git stage ./backend/be-deployment.yaml
        - git commit -m "Update the backend image tag [skip-ci]"
        - git push origin HEAD:$CI_COMMIT_REF_NAME
```
**Frontend**
```yml
stages:
  - build
  - update-manifest
docker_build:
  image: docker:24.0.7
  stage: build
  variables:
    APP_NAME: "first-todo-app-"
    DOCKER_IMAGE: $DOCKER_USER_NAME/${$APP_NAME}${CI_PROJECT_NAME}:$CI_COMMIT_SHA
  before_script:
    - docker login -u $DOCKER_USER_NAME -p $DOCKER_PASSWORD
  script:
    - echo $DOCKER_PASSWORD
    - docker build -t $DOCKER_IMAGE -f backend.dockerfile .
    - docker push $DOCKER_IMAGE
  
update-manifest:
    stage: update-manifest
    variables:
        APP_NAME: "first-todo-app-"
        DOCKER_IMAGE: $DOCKER_USER_NAME/${$APP_NAME}${CI_PROJECT_NAME}:$CI_COMMIT_SHA
        MANIFEST_GIT_REPO: https://$USERNAME:$PAT_TOKEN@git.gebeya.training/devops-ci-cd/argocd-first-test.git
    before_script:
        - apk add git
        - git clone $MANIFEST_GIT_REPO
        - git config --global user.name "$GITLAB_USER_NAME"
        - git config --global user.email "$GITLAB_USER_EMAIL"
    script:
        - cd argocd-first-test
        - git remote set-url origin --push $MANIFEST_GIT_REPO
        - |
            sed -i "s/image:.*/image: $DOCKER_IMAGE/g" ./frontend/fe-deployment.yaml
        - git stage ./frontend/be-deployment.yaml
        - git commit -m "Update the backend image tag [skip-ci]"
        - git push origin HEAD:$CI_COMMIT_REF_NAME
```
**Note**: For the pipeline to work correctly the following environment variables need to be defined in the GitLab Settings page
- DOCKER_USER_NAME: Your dockerhub username
- DOCKER_PASSWORD: your dockerhub password
- USERNAME: your GitLab username
- PAT_TOKEN: generated personal access token from the GitLab settings page
## Add your project to ArgoCD
Refer the argocd document attached in this repo
## Make Changes to the app and see the magic
Update the code for the frontend as below
Add the following just above the `<title>` section in the `public/index.html` file
```html
<link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
```
Substitute the body tag with the following
```html
<body class="bg-gray-100 font-sans">
```
Substitute `<div id="root"></div>` with
```html
<div id="root" class="max-w-2xl mx-auto p-4">
```
Update the section `<header> . . .</header>` in the file `src/components/Header.js` with 
```html
<header class="mb-4"> <h1 class="text-3xl font-bold">Welcome to Frontend of the TodoList application!</h1> <p>Source code can be found at the <a href="https://github.com/fif911/k8app" class="text-blue-500">GitHub repo</a></p> <p class="text-green-600">Version 1.4 🥳🥳🥳🥳🥳</p> <p class="text-indigo-600">This one cooler 😎😎😎😎😎😎</p> </header>
```
and commit the changes, and push. The pipeline should trigger and do the magic, finally wait for 3min to see your changes or go to argocd then open your application and click on refresh, the change will be automatically picked up.
Now go to your browser and refresh the frontend page.
