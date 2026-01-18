pipeline {
    agent any

    tools {
        nodejs 'node'  
    }

    environment {
        IMAGE_VERSION = "v1.0"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Set ENV') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.PORT = '3000'
                        env.IMAGE_NAME = "nodemain:${IMAGE_VERSION}"
                    } else {
                        env.PORT = '3001'
                        env.IMAGE_NAME = "nodedev:${IMAGE_VERSION}"
                    }

                    echo "Branch: ${env.BRANCH_NAME}"
                    echo "Port: ${env.PORT}"
                    echo "Image: ${env.IMAGE_NAME}"
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watch=false'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${env.IMAGE_NAME} .
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                docker rm -f app_${env.BRANCH_NAME} || true
                docker run -d \
                  --name app_${env.BRANCH_NAME} \
                  -p ${env.PORT}:3000 \
                  ${env.IMAGE_NAME}
                """
            }
        }
    }
}
