pipeline {
    agent any

    environment {
        IMAGE_NAME = "yourdockerhubusername/myapp:latest"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/yourusername/docker-app.git'
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

        stage('Tag Docker Image') {
            steps {
                sh 'docker tag myapp:latest $IMAGE_NAME'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 8081:80 $IMAGE_NAME'
            }
        }
    }
}
