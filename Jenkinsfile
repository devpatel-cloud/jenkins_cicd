pipeline {
    agent any

    environment {
        IMAGE_NAME = "devpatelcloud/student-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/devpatel-cloud/jenkins_cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME:$TAG .
                docker tag $IMAGE_NAME:$TAG $IMAGE_NAME:latest
                '''
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'devpatelcloud',
                    passwordVariable: 'Devpatel@392006'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME:$TAG
                    docker push $IMAGE_NAME:latest
                    docker logout
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop student-app || true
                docker rm student-app || true
                docker pull $IMAGE_NAME:latest

                docker run -d \
                  --name student-app \
                  -p 80:80 \
                  --restart unless-stopped \
                  $IMAGE_NAME:latest
                '''
            }
        }
    }
}
