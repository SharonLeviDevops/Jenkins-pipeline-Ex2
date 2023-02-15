pipeline {
    agent any

    stages {
        stage('Unittest') {
            steps {
                sh 'python3 -m pytest --junitxml results.xml tests'
            }
        }
        stage('Lint') {
            steps {
                echo "linting"
            }
        }
        stage('Functional test') {
            steps {
                echo "testing"
            }
        }
    post {
    always {
        junit allowEmptyResults: true, testResults: 'results.xml'
       }
      }
    }
}