pipeline {
  agent any
 
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    IMAGE = 'iamwaseem9746/devops-node-webapp'
  }
 
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main',
        url: 'https://github.com/Waseem-Ansari/devops-node-webapp.git'
      }
    }
 
    stage('Build Docker Image') {
      steps {
        script {
docker.build("${IMAGE}:${BUILD_NUMBER}")
        }
      }
    }
 
    stage('Push to DockerHub') {
      steps {
        script {
          docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
            echo "Using Dockerhub creds ID: ${DOCKERHUB_CREDENTIALS}"
            docker.image("${IMAGE}:${BUILD_NUMBER}").push()
          }
        }
      }
    }
 
    stage('Deploy to K8s') {
      steps {
        sh """
          helm upgrade --install webapp ./helm/mywebapp \
            --set image.repository=${IMAGE} \
            --set image.tag=${BUILD_NUMBER}
            --kubeconfig=/home/ec2-user/k3s.yaml \
            --insecure-skip-tls-verify \
            --debug
        """
      }
    }
  }
}
