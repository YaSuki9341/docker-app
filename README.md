Step 1: Verify Prerequisites

Check that Git, Docker, and Jenkins are installed.

git --version
docker --version
systemctl status jenkins

If Jenkins is not running:

sudo systemctl start jenkins
sudo systemctl enable jenkins
Step 2: Create a Sample Application

Create a project folder.

mkdir docker-app
cd docker-app

Create a simple HTML page.

nano index.html

Paste:

<!DOCTYPE html>
<html>
<head>
    <title>Jenkins CI/CD Demo</title>
</head>
<body>
    <h1>Hello from Jenkins CI/CD Pipeline!</h1>
</body>
</html>

Save the file.

Step 3: Create the Dockerfile
nano Dockerfile

Paste:

FROM nginx
COPY . /usr/share/nginx/html

Save the file.

Step 4: Initialize Git Repository
git init

Add the files:

git add .

Commit:

git commit -m "Initial commit"
Step 5: Create a GitHub Repository

Create a repository on GitHub (for example, docker-app).

Add it as the remote:

git remote add origin https://github.com/<username>/docker-app.git

Push the code:

git branch -M main
git push -u origin main

Replace <username> with your GitHub username.

Step 6: Open Jenkins

Open your browser:

http://localhost:8080

(or http://<server-ip>:8080 if Jenkins is on another machine).

Log in to Jenkins.

Step 7: Create a Pipeline Job
Click New Item.
Enter a name, for example, docker-pipeline.
Select Pipeline.
Click OK.
Step 8: Configure the Git Repository

In the Pipeline job:

Scroll to Pipeline.
Choose Pipeline script from SCM.
SCM: Git.
Repository URL:
https://github.com/<username>/docker-app.git
Branch:
*/main
Step 9: Create the Jenkinsfile

Create the file:

nano Jenkinsfile

Paste:

pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/<username>/docker-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Test Application') {
            steps {
                sh 'echo Running Tests'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker tag myapp:latest <dockerhub-username>/myapp:latest'
                sh 'docker push <dockerhub-username>/myapp:latest'
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 8080:80 <dockerhub-username>/myapp:latest'
            }
        }

    }
}

Replace:

<username> with your GitHub username.
<dockerhub-username> with your Docker Hub username.

Commit and push the Jenkinsfile:

git add Jenkinsfile
git commit -m "Added Jenkins pipeline"
git push
Step 10: Configure Docker Hub Credentials (Required)

If your Docker Hub repository is private or you want the pipeline to push images, log in on the Jenkins machine:

docker login

Enter:

Docker Hub username
Password or Personal Access Token
Step 11: Run the Pipeline

In Jenkins:

Open docker-pipeline.
Click Build Now.
Step 12: Monitor the Build

Click the build number.

Open Console Output.

You should see the stages execute:

Cloning repository...

Building Docker image...

Running Tests

Pushing Docker image...

Deploying Docker container...
Step 13: Verify the Docker Image
docker images

Example:

REPOSITORY      TAG
myapp           latest
username/myapp  latest
Step 14: Verify the Running Container
docker ps

Expected:

CONTAINER ID   IMAGE                  PORTS
xxxxxxx        username/myapp:latest  0.0.0.0:8080->80/tcp
Step 15: Test the Application

Open:

http://localhost:8080

or

curl http://localhost:8080

You should see:

Hello from Jenkins CI/CD Pipeline!
