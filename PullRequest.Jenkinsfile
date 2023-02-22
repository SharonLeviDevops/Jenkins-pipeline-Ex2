
pipeline {
    agent any

    stages {
        stage('Lint') {
            steps {
                sh 'python3 -m pylint -f parseable --reports=no *.py > pylint.log'
            }
            post {
                always {
                    sh 'cat pylint.log'
                    recordIssues (
                        enabledForFailure: true,
                        aggregatingResults: true,
                        tools: [pyLint(pattern: '**/pylint.log', toolName: 'Pylint')]
                    )
                }
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
                            python --version
                            python3 --version
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
