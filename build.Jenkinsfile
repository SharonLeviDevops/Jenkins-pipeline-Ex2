pipeline {
    agent any
    stages {
      stage('Build Yolo5 app') {
        steps {
           sh '''
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 700935310038.dkr.ecr.us-east-1.amazonaws.com
                docker build -t sharon-jenkins-yolo:0.1 .
                docker tag sharon-jenkins-yolo:0.1 700935310038.dkr.ecr.us-east-1.amazonaws.com/sharon-jenkins-yolo:0.1
                docker push 700935310038.dkr.ecr.us-east-1.amazonaws.com/sharon-jenkins-yolo:0.1
           '''
       }
     }
     post {
        always {
            docker image prune -a -f
            docker images
        }
    }
     stage('Env') {
          environment {
            AWS_REGION = 'us-east-1'
            ECR_REGION_URL = '700935310038.dkr.ecr.us-east-1.amazonaws.com'
       }
       steps {
          sh '''
            # Your deploy commands here
            '''
      }
    }
  }
}
