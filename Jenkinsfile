pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
    }
    stages {
        stage('Setup') {
            steps {
                dir('jenkins_project') { // Navigate into the project directory
                    script {
                        if (!fileExists("${env.WORKSPACE}\\${VIRTUAL_ENV}")) {
                            bat "python -m venv ${VIRTUAL_ENV}" // Create virtual environment
                        }
                        bat "${VIRTUAL_ENV}\\Scripts\\activate && pip install -r requirements.txt"
                    }
                }
            }
        }
        stage('Lint') {
            steps {
                dir('jenkins_project') {
                    script {
                        bat "${VIRTUAL_ENV}\\Scripts\\activate && flake8 app.py"
                    }
                }
            }
        }
        stage('Test') {
            steps {
                dir('jenkins_project') {
                    script {
                        bat "${VIRTUAL_ENV}\\Scripts\\activate && pytest"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                dir('jenkins_project') {
                    script {
                        echo "Deploying application..."
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
