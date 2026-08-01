---
title: "Jenkins on Arch Linux: How I Set It Up on i3"
author: "Saad"
date: 2026-02-09
tags: ["jenkins", "devops", "arch-linux", "ci-cd", "automation"]
categories: ["DevOps"]
---
## Why I Wrote This

I run Arch Linux with i3 and wanted Jenkins locally for CI/CD experiments. This covers Java 21, the Jenkins install, a test job, and a GitHub pipeline — exactly what I ran on my machine.

---

## 1. What is Jenkins? (Simple Explanation)

**Jenkins is an automation server.**

You can tell Jenkins to:

- Get your code (for example from GitHub)
- Run commands (build, test, scripts)
- Show you if things passed or failed

Instead of you running commands manually every time, Jenkins does it **automatically**.

You can think of Jenkins as:

> A small robot that runs your scripts in a safe and repeatable way.

---

## 2. Java Requirement (Very Important)

Jenkins is a Java application.  
It needs a **stable, Long-Term Support (LTS)** version of Java.

### Recommended Java Versions

| Java Version | Type    | Recommendation      |
| ------------ | ------- | ------------------- |
| Java 17      | LTS     | Stable — works well |
| Java 21      | LTS     | Best choice         |
| Java 25      | Non-LTS | Not recommended     |

In this guide we use **Java 21 (LTS)** on Arch.

---

## 3. Remove Wrong Java Version (If Needed)

First, check which Java version you have:

```bash
java -version
```

If you see something like Java 25 or a non‑LTS version, remove it:

```bash
# Remove the current OpenJDK package (example: jdk-openjdk)
sudo pacman -Rns jdk-openjdk

# Optionally clean unused dependencies
sudo pacman -Rns $(pacman -Qtdq)
```

If you already have Java 21 installed, you can skip removal.

---

## 4. Install Java 21 (LTS) on Arch

Install Java 21 JDK:

```bash
sudo pacman -S jdk21-openjdk
```

Verify the version:

```bash
java -version
```

You should see something like:

```text
openjdk version "21.x.x" 20xx-xx-xx
```

If the version shows `21`, you are good.

---

## 5. Install Jenkins on Arch Linux

Arch Linux has a `jenkins` package.

Install it:

```bash
sudo pacman -S jenkins
```

During installation you may see something like:

```text
There are 2 providers available for java-runtime=21:
1) jdk21-openjdk
2) jre21-openjdk
```

Choose:

```text
1
```

This means Jenkins will use the JDK 21 runtime.

---

## 6. Start and Enable Jenkins Service

Start Jenkins now:

```bash
sudo systemctl start jenkins
```

Enable Jenkins to start on boot:

```bash
sudo systemctl enable jenkins
```

Check that it is running:

```bash
systemctl status jenkins
```

You should see something like:

```text
Active: active (running)
```

If it is not active, read the last few lines of the status output for errors.

---

## 7. Open Jenkins in Your Browser

By default Jenkins listens on port **8080**.

Open your browser and go to:

```text
http://localhost:8080
```

You should see the Jenkins unlock screen.

If you cannot open it:

- Check that Jenkins is running: `systemctl status jenkins`
- Check firewall rules (if you use one)

---

## 8. Get the Jenkins Unlock Password

On the first run, Jenkins asks for an **initial admin password**.

Get it with:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the long string and paste it into the browser field.

Click **Continue**.

---

## 9. Jenkins Initial Setup

After unlock, Jenkins will ask you how to set it up.

1. Click **Install suggested plugins**
2. Wait until plugins finish installing
3. Create your **admin user** (username, password, email)
4. Click **Save and Finish**
5. Click **Start using Jenkins**

Now you are on the main **Jenkins Dashboard**.

---

## 10. Create a Simple Freestyle Job

We will create a tiny job that prints some information.  
This proves Jenkins is working correctly.

### 10.1 Create the Job

1. On the Jenkins dashboard, click **New Item**
2. In **Item name**, type: `hello-jenkins`
3. Select **Freestyle project**
4. Click **OK**

You are now in the job configuration page.

### 10.2 Add a Build Step

Scroll down to the **Build** section.

1. Click **Add build step → Execute shell**
2. In the **Command** box, paste:

```bash
echo "Hello Jenkins!"
echo "Running on Arch Linux"
date
whoami
```

3. Click **Save**

---

## 11. Run the Freestyle Job

From the job page for `hello-jenkins`:

1. Click **Build Now**
2. On the left side, you will see a build number, for example `#1`
3. Click on `#1`
4. Click **Console Output**

You should see output similar to:

```text
Hello Jenkins!
Running on Arch Linux
Tue Feb 10 13:00:00 PKT 2026
jenkins
```

If you see this, your first Jenkins job is working correctly.

---

## 12. Where Jenkins Stores Its Files (Useful Paths)

Jenkins uses these main paths on Arch:

| Purpose        | Path                             |
| ------------- | -------------------------------- |
| Jenkins home  | `/var/lib/jenkins`              |
| Jobs          | `/var/lib/jenkins/jobs/`        |
| Job workspace | `/var/lib/jenkins/workspace/`   |
| Config        | `/etc/conf.d/jenkins`           |
| Logs          | `/var/log/jenkins/`             |

For example, to see files for your job:

```bash
ls /var/lib/jenkins/jobs/hello-jenkins/
```

---

## 13. (Optional) When Jenkins Uses a Git Repo

When Jenkins checks out a Git repository for a job, it places it in the **workspace** folder:

```text
/var/lib/jenkins/workspace/<job-name>/
```

Example for our later pipeline job:

```text
/var/lib/jenkins/workspace/github-pipeline/
```

This is useful when you want to debug or inspect the code that was built.

---

## 14. Common Jenkins Service Commands

Some handy commands for daily use:

```bash
# Restart Jenkins (after config changes)
sudo systemctl restart jenkins

# Stop Jenkins
sudo systemctl stop jenkins

# Start Jenkins
sudo systemctl start jenkins

# Follow Jenkins logs in real-time
journalctl -u jenkins -f
```

---

## 15. Jenkins Pipeline + GitHub (Realistic Example)

Now we will set up a **real Jenkins Pipeline** that:

- Uses a **GitHub repository**
- Stores the pipeline in a **Jenkinsfile** inside the repo
- Lets Jenkins pull and run the pipeline automatically

This is how Jenkins is used in real projects.

### 15.1 What We Will Build

On GitHub we will have:

```text
jenkins-pipeline-demo
├── Jenkinsfile
└── app.sh
```

Jenkins will:

1. Clone the repo
2. Read the `Jenkinsfile`
3. Run the script `app.sh` as part of the pipeline

---

## 16. Install Git (If Not Already Installed)

On Arch:

```bash
sudo pacman -S git
```

Check Git:

```bash
git --version
```

If you see a version number, Git is installed correctly.

---

## 17. Create the GitHub Repository

1. Go to GitHub and log in
2. Click **New repository**
3. Name it: `jenkins-pipeline-demo`
4. Choose **Public** (easier while learning)
5. **Do not** initialize with a README
6. Click **Create repository**

Copy the repo URL, for example:

```text
https://github.com/ayyzenn/jenkins-pipeline-demo.git
```

---

## 18. Prepare the Repo Locally

In your terminal:

```bash
cd ~
mkdir jenkins-pipeline-demo
cd jenkins-pipeline-demo
git init
```

### 18.1 Create a Simple Script

Create a small script file:

```bash
nano app.sh
```

Paste:

```bash
#!/bin/bash
echo "Hello from GitHub + Jenkins Pipeline"
uname -a
date
```

Save and exit. Then make it executable:

```bash
chmod +x app.sh
```

---

## 19. Create the Jenkinsfile (Important)

Now create a `Jenkinsfile` in the same folder:

```bash
nano Jenkinsfile
```

Paste this pipeline definition:

```groovy
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo 'Repository cloned by Jenkins'
            }
        }

        stage('Run Script') {
            steps {
                sh './app.sh'
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
```

Save and exit.

This file tells Jenkins **what to do** when the pipeline runs.

---

## 20. Push the Code to GitHub

Back in the `jenkins-pipeline-demo` folder:

```bash
git add .
git commit -m "Initial Jenkins pipeline"
git branch -M main
git remote add origin https://github.com/ayyzenn/jenkins-pipeline-demo.git
git push -u origin main
```

Now GitHub has:

- `app.sh`
- `Jenkinsfile`

---

## 21. Create a GitHub Token for Jenkins

Jenkins needs permission to pull from your private or API-protected repos.

1. On GitHub, go to **Settings → Developer settings → Personal access tokens**
2. Create a new token (classic or fine-grained)
3. Give it **repo** access
4. Copy the token (keep it secret)

You will use this token as a password in Jenkins credentials.

---

## 22. Add GitHub Credentials to Jenkins

In Jenkins:

1. Go to **Manage Jenkins → Credentials**
2. Select a **(global)** domain (or create one)
3. Click **Add Credentials**

Fill in:

- **Kind**: `Username with password`
- **Username**: your GitHub username
- **Password**: the GitHub token you created
- **ID**: `github-token` (easy to remember)

Click **OK / Save**.

---

## 23. Create a Jenkins Pipeline Job (From SCM)

Back in the Jenkins dashboard:

1. Click **New Item**
2. Name it: `github-pipeline`
3. Select **Pipeline**
4. Click **OK**

You are now in the job configuration.

---

## 24. Configure the Pipeline (From Git)

Scroll down to the **Pipeline** section.

Set:

- **Definition**: `Pipeline script from SCM`
- **SCM**: `Git`

Then fill in:

- **Repository URL**:

  ```text
  https://github.com/ayyzenn/jenkins-pipeline-demo.git
  ```

- **Credentials**: select `github-token`
- **Branch Specifier**:

  ```text
  */main
  ```

- **Script Path**:

  ```text
  Jenkinsfile
  ```

Click **Save**.

Now Jenkins knows:

- Which repo to clone
- Which branch to use
- Which file (`Jenkinsfile`) describes the pipeline

---

## 25. Run the Jenkins Pipeline

From the `github-pipeline` job page:

1. Click **Build Now**
2. Click the build number (for example `#1`)
3. Click **Console Output**

You should see logs similar to:

```text
Cloning the remote Git repository
Repository cloned by Jenkins
[Pipeline] sh
+ ./app.sh
Hello from GitHub + Jenkins Pipeline
... (system info and date) ...
Pipeline finished successfully
Finished: SUCCESS
```

If you see `Finished: SUCCESS`, your GitHub-based Jenkins Pipeline is working.

---

## 26. What You Learned (Summary)

You now know how to:

- Install **Java 21** and **Jenkins** on Arch Linux
- Start and manage the `jenkins` systemd service
- Create a **freestyle job** that runs shell commands
- Understand where Jenkins stores its **jobs** and **workspaces**
- Put your **pipeline definition in a GitHub repo** as a `Jenkinsfile`
- Create a Jenkins **Pipeline job** that reads from SCM (Git)

This is the same pattern teams use in real projects:

- Code + `Jenkinsfile` live in Git
- Jenkins pulls the repo
- Jenkins runs the pipeline defined in the repo