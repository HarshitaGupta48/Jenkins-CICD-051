```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "harshitagupta0408/student-app051"
        TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/HarshitaGupta48/Jenkins-CICD-051.git'
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
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        set +x
                        echo "$DOCKER_PASS" | docker login \
                            -u "$DOCKER_USER" --password-stdin
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
                    docker rm -f student-app || true
                    docker pull $IMAGE_NAME:latest
                    docker run -d \
                        --name student-app \
                        -p 8081:80 \
                        --restart unless-stopped \
                        $IMAGE_NAME:latest
                    docker ps
                '''
            }
        }
    }

    post {
        success {
            echo 'Build, Push and Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed. Check Console Output.'
        }
    }
}
```
