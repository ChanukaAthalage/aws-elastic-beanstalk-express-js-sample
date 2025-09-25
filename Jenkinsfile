pipeline {
  agent any

  environment {
    // Docker-in-Docker (DinD) connection info
    DOCKER_HOST = 'tcp://docker:2376'
    DOCKER_TLS_VERIFY = '1'
    DOCKER_CERT_PATH = '/certs/client'

    IMAGE_NAME = '21953004@student.curtin.edu.au/aws-nodejs-sample'
    IMAGE_TAG  = "v${env.BUILD_NUMBER}"
  }

  options {
    // Keep logs manageable
    buildDiscarder(logRotator(numToKeepStr: '15', daysToKeepStr: '14'))
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps {
        // Pull code from GitHub repo
        checkout scm
      }
    }

    stage('Build & Test (Node 16)') {
      // Use Node 16 Docker image for build and testing
      agent { docker { image 'node:16'; args '-u root:root' } }
      steps {
        sh 'node -v && npm -v'
        sh 'npm install --save'   // install app dependencies
        sh 'npm test'             // run unit tests
      }
    }

    stage('Security Scan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh '''
            npm install -g snyk
            snyk auth "$SNYK_TOKEN"
            snyk test --severity-threshold=high
          '''
        }
      }
    }

    stage('Docker Build & Push') {
      environment {
        REGISTRY_CREDS = credentials('dockerhub-credentials')
      }
      steps {
        sh '''
          docker version
          echo "$REGISTRY_CREDS_PSW" | docker login -u "$REGISTRY_CREDS_USR" --password-stdin
          docker build -t $IMAGE_NAME:$IMAGE_TAG .
          docker push $IMAGE_NAME:$IMAGE_TAG
        '''
      }
    }
  }

  post {
    always {
      // Archive logs and test reports (if any)
      archiveArtifacts artifacts: 'npm-debug.log, **/junit*.xml', allowEmptyArchive: true
    }
  }
}
