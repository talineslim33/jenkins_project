pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
        PYTHON_PATH = 'C:\\Python312\\python.exe'
        PYTHONPATH = "${env.WORKSPACE}\\jenkins_project"  // Set the PYTHONPATH to the jenkins_project directory
        PYTHONIOENCODING = 'utf-8'
    }
    stages {
        stage('Setup') {
            steps {
                script {
                    if (!fileExists("${env.WORKSPACE}\\${VIRTUAL_ENV}")) {
                        bat "${PYTHON_PATH} -m venv ${VIRTUAL_ENV}"
                    }
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        ${PYTHON_PATH} -m pip install --upgrade pip
                        ${PYTHON_PATH} -m pip install -r jenkins_project\\requirements.txt
                    """
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        ${PYTHON_PATH} -m pip freeze
                    """
                }
            }
        }
        stage('Lint') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        ${PYTHON_PATH} -m flake8 jenkins_project\\app.py
                    """
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        set PYTHONPATH=${PYTHONPATH} && ${PYTHON_PATH} -m pytest jenkins_project\\tests
                    """
                }
            }
        }
        stage('Code Coverage') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        ${PYTHON_PATH} -m pip install coverage
                        set PYTHONPATH=${PYTHONPATH} && ${PYTHON_PATH} -m coverage run -m pytest jenkins_project\\tests
                        ${PYTHON_PATH} -m coverage report
                        ${PYTHON_PATH} -m coverage html
                    """
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        ${PYTHON_PATH} -m bandit -r jenkins_project --quiet --exit-zero > bandit_report.txt 2>&1
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                }
            }
        }
    }
    post {
        always {
            script {
                if (fileExists('bandit_report.txt')) {
                    echo 'Bandit Report:'
                    bat "type bandit_report.txt"
                }
            }
            cleanWs()
        }
    }
}
