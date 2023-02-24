pipeline {
    agent {
        docker {
            image '700935310038.dkr.ecr.us-east-1.amazonaws.com/sharon-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
      }
    parameters { string(name: 'YOLO5_IMAGE_URL', defaultValue: '', description: '') }
    stages {
        stage('Deploy') {
            steps {
                sh '# kubectl apply -f deploy.yaml'
            }
        }
    }
  }
