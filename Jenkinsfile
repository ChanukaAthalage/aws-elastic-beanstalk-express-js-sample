pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'docker run --rm -v $PWD:/app -w /app node:16 npm install --save'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'docker run --rm -v $PWD:/app -w /app node:16 npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-app .'
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
