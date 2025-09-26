pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = credentials('docker-hub-user')
        DOCKER_HUB_PASS = credentials('docker-hub-pass')
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
                    snyk/snyk:docker snyk test node-app --severity-threshold=high
                '''
            }
        }
        stage('Docker Build & Push') {
            steps {
                sh '''
                  echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin
                  docker tag node-app $DOCKER_HUB_USER/node-app:latest
                  docker push $DOCKER_HUB_USER/node-app:latest
                '''
            }
        }
    }
}
