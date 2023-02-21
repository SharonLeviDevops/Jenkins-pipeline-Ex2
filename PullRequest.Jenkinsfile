pipeline {
agent any

stages {
    stage('Lint') {
        steps {
            sh 'python3 --version'
        }
    }
    stage('Unit Tests') {
        steps {
            sh '''
                pip install -r requirements.txt
                python -m pytest --junitxml results.xml tests
                python3 --version
            '''
            }
        }
    stage('Functional Tests') {
        steps {
            echo 'Functional tests running.....'
        }
    }

   }
 }