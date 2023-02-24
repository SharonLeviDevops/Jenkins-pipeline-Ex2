pipeline {
    agent {
        docker {
            sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 700935310038.dkr.ecr.us-east-1.amazonaws.com'
            image '700935310038.dkr.ecr.us-east-1.amazonaws.com/sharon-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    options {
        timeout(time: 45, unit: 'MINUTES')
        retry(1)
        disableConcurrentBuilds()
    }
    environment {
        AWS_REGION = 'us-east-1'
        ECR_REGION_URL = '700935310038.dkr.ecr.us-east-1.amazonaws.com'
        IMAGE_NAME = 'sharon-jenkins-yolo'
    }
    stages {
        stage('Build Yolo5 app') {
            steps {
                sh '''
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $ECR_REGION_URL
                    docker build -t $IMAGE_NAME:$BUILD_NUMBER .
                '''
            }
        }
        stage('Scan images') {
            steps {
                withCredentials([string(credentialsId: 'snyk_token', variable: 'SNYK_TOKEN')]) {
                    sh '''
                        snyk auth $SNYK_TOKEN
                        snyk config set disableSuggestions=true
                        #snyk container test $IMAGE_NAME:$BUILD_NUMBER --severity-threshold=high --file=Dockerfile
                       '''
                }
            }
        }
        stage('Tag and Push') {
            steps {
                sh '''
                    docker tag $IMAGE_NAME:$BUILD_NUMBER $ECR_REGION_URL/$IMAGE_NAME:$BUILD_NUMBER
                    docker push $ECR_REGION_URL/$IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
        stage('Trigger Deploy') {
            steps {
                build job: 'AppDeploy', wait: false, parameters: [
                    string(name: 'YOLO5_IMAGE_URL', value: "$ECR_REGION_URL/$IMAGE_NAME:$BUILD_NUMBER")
                ]
            }
        }
    }
    post {
        always {
            sh 'docker image prune -f'
        }
    }
}