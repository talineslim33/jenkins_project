pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
    }
    stages {
        stage('Setup') {
            steps {
                dir('jenkins_project') { // Navigate into jenkins_project subdirectory
                    script {
                        if (!fileExists("${env.WORKSPACE}/${VIRTUAL_ENV}")) {
                            sh "python -m venv ${VIRTUAL_ENV}"
                        }
                        sh "source ${VIRTUAL_ENV}/bin/activate && pip install -r requirements.txt"
                    }
                }
            }
        }
        stage('Lint') {
            steps {
                dir('jenkins_project') { // Navigate into jenkins_project subdirectory
                    script {
                        sh "source ${VIRTUAL_ENV}/bin/activate && flake8 app.py"
                    }
                }
            }
        }
        stage('Test') {
            steps {
                dir('jenkins_project') { // Navigate into jenkins_project subdirectory
                    script {
                        sh "source ${VIRTUAL_ENV}/bin/activate && pytest"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                dir('jenkins_project') { // Navigate into jenkins_project subdirectory if necessary
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
