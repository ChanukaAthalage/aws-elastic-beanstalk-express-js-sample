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
                sh 'docker run --rm node-app npm test'
            }
        }

        stage('Security Scan') {
            steps {
                sh '''
                  npm install -g snyk
                  snyk test --severity-threshold=high
                '''
            }
        }
    }
}
