pipeline {
    agent any

    environment {
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
                    -v /var/run/docker.sock:/var/run/docker.sock \
                    snyk/snyk:docker test node-app --severity-threshold=high
                '''
            }
        }

        stage('Docker Build & Push') {
            steps {
                echo "Next: Push to Docker Hub (configure Docker Hub credentials here)"
                // sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                // sh 'docker tag node-app $DOCKER_USER/node-app:latest'
                // sh 'docker push $DOCKER_USER/node-app:latest'
            }
        }
    }
}
