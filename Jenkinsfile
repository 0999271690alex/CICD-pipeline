pipeline {
    agent any

    environment {
        IMAGE_NAME = "node${env.BRANCH_NAME}:v1.0"
        PORT = ""
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
                        PORT = "3000"
                    } else if (env.BRANCH_NAME == 'dev') {
                        PORT = "3001"
                    }
                    echo "Branch: ${env.BRANCH_NAME}"
                    echo "Port: ${PORT}"
                }
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watch=false'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                sh """
                docker rm -f ${env.BRANCH_NAME} || true
                docker run -d \\
                  --name ${env.BRANCH_NAME} \\
                  -p ${PORT}:3000 \\
                  ${IMAGE_NAME}
                """
            }
        }
    }
}
