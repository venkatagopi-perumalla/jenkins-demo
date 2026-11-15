# jenkins-demo

Two small Jenkins pipelines: one triggered by a GitHub webhook, and one that runs each stage in its own Docker container.

## 1. Webhook pipeline (`Jenkinsfile`)

Every push to this repo triggers a Jenkins build through a GitHub webhook. No polling is involved.

| Stage | What it does |
|---|---|
| Show commit | Prints the commit Jenkins checked out and lists the workspace |
| Run script | Runs `hello.sh`, which prints the user, date and Jenkins build number |

A `post` block reports whether the run passed or failed.

**Setup:** create a Pipeline job in Jenkins pointing at this repo (Pipeline script from SCM), tick **GitHub hook trigger for GITScm polling**, and add a webhook in the GitHub repo settings pointing to `http://<jenkins-host>/github-webhook/`.

## 2. Per-stage Docker agents (`multi-stage/Jenkinsfile`)

The pipeline uses `agent none`, and each stage picks its own container, so the Jenkins server needs only Docker, not every toolchain:

| Stage | Container image | Check |
|---|---|---|
| Backend | `maven:3.9-eclipse-temurin-17` | `mvn --version` |
| Frontend | `node:20-alpine` | `node --version`, `npm --version` |
| Database | `mysql:8.0` | `mysql --version` |

**Setup:** install the Docker Pipeline plugin, give the Jenkins user access to Docker, and set the job's script path to `multi-stage/Jenkinsfile`.

## What I'd do differently today

In my newer projects I use GitHub Actions for the same ideas, with real build, test and security-scan stages. See [learning-tracker](https://github.com/venkatagopi-perumalla/learning-tracker) and [eks-gitops-platform](https://github.com/venkatagopi-perumalla/eks-gitops-platform).
