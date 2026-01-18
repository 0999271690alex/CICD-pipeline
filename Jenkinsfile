pipeline {
    agent any

    tools {
        nodejs 'node'
    }

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
                        env.PORT = "3000"
                    } else {
                        env.PORT = "3001"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh '''
                  node -v
                  npm -v
                  npm install
                  npm run build
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'CI=true npm test'
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
                docker run -d \
                  --name ${env.BRANCH_NAME} \
                  -p ${PORT}:3000 \
                  ${IMAGE_NAME}
                """
            }
        }
    }
}
