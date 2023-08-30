pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    IMAGE_NAME = "drupal-latest"
    DOCKERHUB_REPO = "priya20xenonstack"
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t ${IMAGE_NAME} .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
   stage('Push') {
      steps {
        script{
          def lastSuccessfulBuild = currentBuild.getPreviousSuccessfulBuild()?.number ?:1
         
          print ""

          def oldTag = sh(script: "awk '/docker tag/' /var/jenkins_home/jobs/argo-test/branches/master/builds/${lastSuccessfulBuild}/log | cut -d ' ' -f 5 | head -n 2 | tail -n 1 | cut -d ':' -f 2", returnStdout: true)
          def intValue = oldTag.toInteger()
          def newTag = (intValue + 1).toString()
          echo "Old tag: ${oldTag} New tag: ${newTag}"

          print "Tag ${newTag}"
          
          def dockerImage = "${DOCKERHUB_REPO}/${IMAGE_NAME}:${newTag}" 
          
          //print dockerImage
          sh "docker build -t ${dockerImage} ."
          sh "docker tag ${IMAGE_NAME} ${dockerImage}"
          sh "docker push ${dockerImage}"

          sh """
                cat argoproj/argocheck.yaml | sed "s/image:.*/image: priya20xenonstack\\/docker-latest:${newTag}/g" > argocheck.yaml.new
          """
          
          sh """
              ls -la
              pwd
              mv "argocheck.yaml.new" "argoproj/argocheck.yaml" && echo "Deployment file updated"
            """
      }
    }
  }
}
  post {
    always {
      sh 'docker logout'
    }
  }
}
     
