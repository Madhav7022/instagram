pipeline {
    agent any

    environment {
        REGISTRY = "your-dockerhub-username"
        IMAGE = "spring-boot-app"
        CONTAINER_NAME = "springboot-container"
        DEPLOY_SERVER = "app-server-ip"
        DEPLOY_USER = "deployuser"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo/spring-boot-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t $REGISTRY/$IMAGE:${BUILD_NUMBER} ."
            }
        }

        stage('Push to Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push $REGISTRY/$IMAGE:${BUILD_NUMBER}"
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['deploy-server-ssh']) {
                    sh """
                    ssh $DEPLOY_USER@$DEPLOY_SERVER \\
                        'docker pull $REGISTRY/$IMAGE:${BUILD_NUMBER} &&
                         docker stop $CONTAINER_NAME || true &&
                         docker rm $CONTAINER_NAME || true &&
                         docker run -d --name $CONTAINER_NAME -p 8080:8080 $REGISTRY/$IMAGE:${BUILD_NUMBER}'
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
