pipeline {
  agent any

  environment {
    REGISTRY = "ghcr.io/devpandai"
    IMAGE_NAME = "mysql"
    IMAGE_TAG = "latest"
    GITOPS_REPO = "https://github.com/devpandai/mysql-deploy.git"
  }

  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/devpandai/mysql.git'
      }
    }

    stage('Build & Push Docker Image') {
      steps {
        script {
          sh """
          docker build -t $REGISTRY/$IMAGE_NAME:$IMAGE_TAG .
          echo $GITHUB_TOKEN | docker login ghcr.io -u devpandai --password-stdin
          docker push $REGISTRY/$IMAGE_NAME:$IMAGE_TAG
          """
        }
      }
    }

    stage('Update GitOps Repo') {
      steps {
        script {
          sh """
          set -e
          rm -rf deploy-repo
          git clone $GITOPS_REPO deploy-repo
          cd deploy-repo

          sed -i 's|image:.*|image: $REGISTRY/$IMAGE_NAME:$IMAGE_TAG|' mysql-deployment.yaml

          git config user.name "jenkins"
          git config user.email "jenkins@local"
          git add .

          if git diff --cached --quiet; then
            echo "No changes to commit"
          else
            git commit -m "update image to $IMAGE_TAG"
            git push
          fi
          """
        }
      }
    }
  }
}
