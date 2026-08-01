---
title: "Jenkins + Docker Pipeline from GitHub"
author: "Saad"
date: 2026-02-10
tags: ["jenkins", "docker", "ci-cd", "github", "devops"]
categories: ["DevOps"]
---
## What I Built

I wanted Jenkins to pull code from GitHub, build a Docker image, and run a container automatically. This post is the pipeline I put together for that.

---

## Goal: From Manual to Automatic

Manually, we usually do this:

1. Clone a GitHub repository
2. Run `docker build`
3. Run `docker run`
4. Check the output

With Jenkins, all of this happens **automatically** when the pipeline runs.

You click **Build Now** (or trigger from GitHub), and Jenkins does the rest.

---

## Prerequisites (Short Version)

You should already have:

- Jenkins installed and working
- Docker installed and working
- Jenkins user allowed to run Docker (for example, by adding the `jenkins` user to the `docker` group)

On many systems this looks like:

**Ubuntu** (after adding your user to the `docker` group):

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

**Arch Linux:**

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

Then log out and log back in, and make sure a simple `docker ps` works from the Jenkins environment.

---

## Repository Structure

My GitHub repository looks like this:

```text
jenkins-pipeline-demo/
├── Jenkinsfile
├── Dockerfile
└── app.sh
```

Each file has a clear role:

- **Jenkinsfile** → Jenkins pipeline steps
- **Dockerfile** → How the Docker image is built
- **app.sh** → Script that runs inside the container

---

## `app.sh`: The Script Inside the Container

This is a simple shell script that prints some information.
It proves that the container actually runs.

```sh
#!/bin/sh

echo "Hello from GitHub + Jenkins Pipeline"
uname -a
date
```

Save this as `app.sh` and make it executable:

```bash
chmod +x app.sh
```

---

## `Dockerfile`: How the Image Is Built

This Dockerfile builds a small image using Alpine Linux:

```Dockerfile
FROM alpine:latest

WORKDIR /app
COPY app.sh .
RUN chmod +x app.sh

CMD ["./app.sh"]
```

Explanation:

- Uses **Alpine Linux**, which is small and fast
- Copies `app.sh` into `/app`
- Makes it executable
- Runs `./app.sh` when the container starts

If you build and run this locally, you should see the same output that Jenkins will show later:

```bash
docker build -t jenkins-demo-app .
docker run --rm jenkins-demo-app
```

---

## `Jenkinsfile`: The Pipeline Definition

This is the Jenkins declarative pipeline stored in the repo:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Code checked out from GitHub'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-demo-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker rm -f jenkins-demo-container || true
                docker run --name jenkins-demo-container jenkins-demo-app
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker ps -a'
        }
    }
}
```

### What This Pipeline Does

- Jenkins automatically checks out code from GitHub (handled by Jenkins when using SCM)
- Builds a Docker image named `jenkins-demo-app`
- Removes any old container with the same name (if it exists)
- Runs a new container named `jenkins-demo-container`
- After the run, prints `docker ps -a` so you can see container status

---

## Put the Code on GitHub

In your local folder:

```bash
git init
git add Jenkinsfile Dockerfile app.sh
git commit -m "Jenkins Docker pipeline demo"
git branch -M main
git remote add origin https://github.com/your-username/jenkins-pipeline-demo.git
git push -u origin main
```

Now your GitHub repo has:

- `Jenkinsfile`
- `Dockerfile`
- `app.sh`

---

## Create the Jenkins Pipeline Job

In Jenkins:

1. Go to **New Item**
2. Name: `docker-pipeline-demo`
3. Type: **Pipeline**
4. Click **OK**

Scroll down to the **Pipeline** section and set:

- **Definition**: `Pipeline script from SCM`
- **SCM**: `Git`
- **Repository URL**: your GitHub repo, for example  
  `https://github.com/your-username/jenkins-pipeline-demo.git`
- **Credentials**: (optional) your GitHub token, if the repo is private
- **Branch Specifier**: `*/main`
- **Script Path**: `Jenkinsfile`

Click **Save**.

Now Jenkins knows where to pull code from and which `Jenkinsfile` to use.

---

## Run the Pipeline and Check Output

From the `docker-pipeline-demo` job page:

1. Click **Build Now**
2. Click the build number (for example `#1`)
3. Click **Console Output**

In the log you should see:

- Git repository being cloned
- Docker image being built
- Docker container being started
- Output from `app.sh`, like:

```text
Hello from GitHub + Jenkins Pipeline
Linux ...
Mon Feb 10 12:34:56 UTC 2026
```

If there are Docker permission errors, check:

- Jenkins user in `docker` group
- Docker daemon is running

---

## Why This Setup Is Important

This pipeline is useful because:

- **No manual Docker commands**: Jenkins runs them for you
- **Same build process every time**: reproducible builds
- **Easy to extend**: you can add tests, security scans, or deployments
- **Works well with GitHub**: every change in Git can trigger the same pipeline

This is the base of many real **CI/CD systems**.

---

## What You Can Add Next

From here, you can extend the pipeline to:

- Push the image to **Docker Hub** or another registry
- Use **Docker Compose** to run multiple services
- Add **test stages** before building or after running the container
- Deploy to a **server**, **VM**, or **Kubernetes**
- Trigger builds using **GitHub webhooks**

---

## Conclusion

Using Jenkins with Docker and GitHub makes automation simple and reliable.

Once this base pipeline works, you can slowly turn it into a **production-grade CI/CD pipeline**:

- Same idea
- Same tools
- Just more stages and checks

This setup is small, but very powerful.