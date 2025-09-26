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
                sh '''
                  echo "Building Docker image..."
                  docker build -t node-app .
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                  echo "Running unit tests inside container..."
                  docker run --rm node-app npm test
                '''
            }
        }

        stage('Security Scan with Snyk') {
            steps {
                sh '''
                  echo "Scanning Docker image for vulnerabilities..."
                  docker run --rm \
                    -e SNYK_TOKEN=$SNYK_TOKEN \
                    -v /var/run/docker.sock:/var/run/docker.sock \
                    snyk/snyk:docker sh -c "snyk test node-app --severity-threshold=high"
                '''
            }
        }

        stage('Docker Hub Push (Optional)') {
            steps {
                echo "This step is optional. Uncomment to enable Docker Hub push."
                // withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                //     sh '''
                //       echo "Logging in to Docker Hub..."
                //       echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                //       docker tag node-app $DOCKER_USER/node-app:latest
                //       docker push $DOCKER_USER/node-app:latest
                //     '''
                // }
            }
        }
    }
}
