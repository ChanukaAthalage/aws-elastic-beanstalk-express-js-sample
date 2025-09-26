pipeline {
    agent any

    environment {
        // Map Jenkins secret text credential with ID 'snyk-token' to SNYK_TOKEN
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'ls -l'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-app .'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'docker run --rm node-app npm test'
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                  docker run --rm \
                    -e SNYK_TOKEN=$SNYK_TOKEN \
                    -v $(pwd):/app \
                    -w /app snyk/snyk:docker snyk test --severity-threshold=high
                '''
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker tag node-app $DOCKER_USER/node-app:latest
                      docker push $DOCKER_USER/node-app:latest
                    '''
                }
            }
        }
    }
}
