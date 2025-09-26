pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root'   // run as root to avoid workspace/permission issues
        }
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --save'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'npm test'
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
