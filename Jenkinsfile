pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'ls -l'   // confirm package.json etc.
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-app .'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                  docker run --rm \
                    -v $WORKSPACE:/app \
                    -w /app \
                    node:16 npm test
                '''
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                  docker run --rm \
                    -v $WORKSPACE:/app \
                    -w /app \
                    -e SNYK_TOKEN=$SNYK_TOKEN \
                    node:16 sh -c "npm install -g snyk && snyk test --severity-threshold=high"
                '''
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds',
                                                 usernameVariable: 'DOCKER_USER',
                                                 passwordVariable: 'DOCKER_PASS')]) {
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
