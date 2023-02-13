pipeline {
    agent any
    options {
        timeout(time: 45, unit: 'MINUTES')
        retry(3)
    }
    environment {
        AWS_REGION = 'us-east-1'
        ECR_REGION_URL = '700935310038.dkr.ecr.us-east-1.amazonaws.com'
        IMAGE_NAME = 'sharon-jenkins-yolo'
        SNYK_TOKEN = 'ebcc3f22-7670-4903-9511-d36c1fcdffb3'
    }
    stages {
        stage('Build Yolo5 app') {
            steps {
                sh '''
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $ECR_REGION_URL
                    docker build -t $IMAGE_NAME:$BUILD_NUMBER .
                    docker tag $IMAGE_NAME:$BUILD_NUMBER $ECR_REGION_URL/$IMAGE_NAME:$BUILD_NUMBER
                    docker push $ECR_REGION_URL/$IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
        stage('Scan') {
            steps {
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh '''
                        snyk auth $SNYK_TOKEN
                        snyk container test $IMAGE_NAME:$BUILD_NUMBER --severity-threshold=high
                    '''
                }
            }
        }
        stage('Post') {
            steps {
                sh '''
                    docker image prune -a -f
                    docker images
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