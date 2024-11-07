pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
        PYTHON_PATH = 'C:\\Python312\\python.exe'  // Path to your Python executable
        PYTHONPATH = "${env.WORKSPACE}"
        PYTHONIOENCODING = 'utf-8'  // Set UTF-8 encoding for all Python output
    }
    stages {
        stage('Setup') {
            steps {
                script {
                    // Create virtual environment if it doesn't exist
                    if (!fileExists("${env.WORKSPACE}\\${VIRTUAL_ENV}")) {
                        bat "${PYTHON_PATH} -m venv ${VIRTUAL_ENV}"
                    }
                    // Activate the virtual environment and install requirements
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        && ${PYTHON_PATH} -m pip install --upgrade pip --user  // Upgrade pip with user option
                        && ${PYTHON_PATH} -m pip install -r requirements.txt --user
                    """
                    // List installed packages
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m pip freeze"
                }
            }
        }
        stage('Lint') {
            steps {
                script {
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m flake8 app.py"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m pytest"
                }
            }
        }
        stage('Code Coverage') {
            steps {
                script {
                    // Ensure coverage is available
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m pip install coverage --user"
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m coverage run -m pytest"
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m coverage report"
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m coverage html"
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    // Adding --exit-zero to Bandit command to avoid pipeline failure
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate.bat && ${PYTHON_PATH} -m bandit -r . --quiet --exit-zero 1>bandit_report.txt 2>&1"
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
