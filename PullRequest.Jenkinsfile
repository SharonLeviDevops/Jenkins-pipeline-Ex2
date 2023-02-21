pipeline {
agent any

stages {
    stage('Lint') {
        steps {
            sh 'python3 -m pylint -f parseable --reports=no *.py > pylint.log'
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
                    python -m pytest --junitxml results.xml tests
                    '''
                }
            }
        }
    }
  }
}
