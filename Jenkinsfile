pipeline {
  agent any

  environment {
    REGISTRY = "ghcr.io/devpandai"
    IMAGE_NAME = "mysql-app"
    IMAGE_TAG = "latest"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/devpandai/mysql-app.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ."
        }
      }
    }

    stage('Push to Registry') {
      steps {
        withCredentials([string(credentialsId: 'github-token', variable: 'TOKEN')]) {
          sh """
            echo $TOKEN | docker login ghcr.io -u devpandai --password-stdin
            docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
          """
        }
      }
    }

  }
}