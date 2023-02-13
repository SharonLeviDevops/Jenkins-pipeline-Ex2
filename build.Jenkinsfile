pipeline {
    agent any
    options {
        timeout(time: 45, unit: 'MINUTES')
        retry(1)
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
                '''
            }
        }
        stage('Scan image') {
            steps {
                withCredentials([string(credentialsId: 'synk', variable: 'SNYK_TOKEN')]) {
                    sh '''
                        snyk-new auth $SNYK_TOKEN
                        snyk-new container test $IMAGE_NAME:$BUILD_NUMBER --severity-threshold=high --file=Docker
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