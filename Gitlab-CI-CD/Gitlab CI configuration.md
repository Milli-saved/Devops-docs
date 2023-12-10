## Step 1: Create a GitLab Project

1. Open GitLab and create a new project.
2. Begin with an empty repository or import a project from a remote repository.
## Step 2: Add CI/CD Configuration

1. After project setup, go to the root directory.
2. Create a file named `.gitlab-ci.yml`.
    - Note: The filename must start with a `dot` (.) and have the extension `.yml`, not `.yaml`.

This configuration file, often referred to as the GitLab CI/CD pipeline file, defines the stages for your project's build, test, and deployment.

By completing these steps, you establish the foundation for an effective CI/CD process in GitLab. Customize your `.gitlab-ci.yml` file to suit your project's specific needs.
# GitLab CI Basic Template

Below is a basic GitLab CI template that you can use to demonstrate the pipeline configuration:

```yaml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building the project..."

test_job:
  stage: test
  script:
    - echo "Running tests..."

deploy_job:
  stage: deploy
  script:
    - echo "Deploying the project..."

```
