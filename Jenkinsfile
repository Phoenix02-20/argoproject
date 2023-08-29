pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    IMAGE_NAME = "argo-test"
    DOCKERHUB_REPO = "priya20xenonstack"
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t ${DOCKERHUB_REPO}/${IMAGE_NAME} .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker tag ${DOCKERHUB_REPO}/${IMAGE_NAME} ${DOCKERHUB_REPO}/${IMAGE_NAME}:0'
        sh 'docker push ${DOCKERHUB_REPO}/${IMAGE_NAME}:0'
        }
        
      }
    }
  post {
    always {
      sh 'docker logout'
    }
  }
}
     
