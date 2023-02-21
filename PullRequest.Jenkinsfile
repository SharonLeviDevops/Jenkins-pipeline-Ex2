pipeline {
agent any

stages {
    stage('Lint') {
        steps {
            sh 'python3 -m pylint -f parseable --reports=no *.py > pylint.log'
        }
    }
    stage('Unit Tests') {
        steps {
            sh '''
                pip install -r requirements.txt
                python -m pytest --junitxml results.xml tests
                python --version
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