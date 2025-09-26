pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'ls -l'   // confirm package.json is present
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'docker run --rm -v $(pwd):/workspace -w /workspace node:16 npm install --save'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'docker run --rm -v $(pwd):/workspace -w /workspace node:16 npm test'
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
