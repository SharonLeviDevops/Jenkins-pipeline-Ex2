pipeline {
agent any

stages {
    stage('Lint') {
        steps {
            sh '''
                    python3 -m pylint -f parseable --reports=no *.py > pylint.log
               '''
        }
    }
    post {
      always {
        sh 'cat pylint.log'
        recordIssues (
         enabledForFailure: true,
         aggregatingResults: true,
         tools: [pyLint(name: 'Pylint', pattern: '**/pylint.log')]
        )
       }
    }
    stage('Tests') {
        when {
            branch 'main'
        }
        failFast true
        parallel {
            stage('Unit Tests') {
                steps {
                    sh '''
                    pip install -r requirements.txt
                    python3 -m pytest --junitxml results.xml tests
                    '''
                }
            }
            stage('Functional Tests') {
                steps {
                    echo 'Functional tests running...'
                }
            }
        }
    }
}
post {
    always {
        junit allowEmptyResults: true, testResults: 'results.xml'
    }
    failure {
        script {
          error('Build failed due to test failures')
    }
   }
  }
}