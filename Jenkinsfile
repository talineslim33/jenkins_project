pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
    }
    stages {
        stage('Setup') {
            steps {
                dir('jenkins_project') {
                    script {
                        if (!fileExists("${VIRTUAL_ENV}")) {
                            bat 'python -m venv venv'
                        }
                        bat """
                            venv\\Scripts\\activate
                            && python -m pip install --upgrade pip
                            && pip install -r requirements.txt
                        """
                    }
                }
            }
        }

        stage('Lint') {
            steps {
                dir('jenkins_project') {
                    bat 'venv\\Scripts\\activate && flake8 app.py'
                }
            }
        }

        stage('Coverage') {
            steps {
                dir('jenkins_project') {
                    bat 'venv\\Scripts\\activate && coverage run -m pytest'
                    bat 'venv\\Scripts\\activate && coverage report -m'
                    bat 'venv\\Scripts\\activate && coverage html'
                }
            }
        }

        stage('Security Scan') {
            steps {
                dir('jenkins_project') {
                    bat 'venv\\Scripts\\activate && bandit -r . -x venv'
                }
            }
        }

        stage('Test') {
            steps {
                dir('jenkins_project') {
                    bat 'venv\\Scripts\\activate && pytest'
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('jenkins_project') {
                    echo 'Deploying application...'
                    // Insert actual deployment steps here
                }
            }
        }
    }
    
    post {
        always {
            dir('jenkins_project') {
                script {
                    if (fileExists('bandit_report.txt')) {
                        echo 'Security Scan (Bandit) Report:'
                        bat "type bandit_report.txt"
                    }
                }
            }
            cleanWs()
        }
    }
}
