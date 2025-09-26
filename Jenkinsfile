pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
                sh 'ls -l'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh 'docker build -t node-app:latest .'
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo "Running unit tests inside container..."
                sh 'docker run --rm node-app:latest npm test'
            }
        }

        stage('Security Scan with Snyk') {
            steps {
                echo "Scanning Docker image for vulnerabilities..."
                sh '''
                  docker run --rm \
                    -e SNYK_TOKEN=$SNYK_TOKEN \
                    -v /var/run/docker.sock:/var/run/docker.sock \
                    snyk/snyk:docker snyk container test node-app:latest --severity-threshold=high
                '''
            }
        }

    }
}
