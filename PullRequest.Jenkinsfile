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