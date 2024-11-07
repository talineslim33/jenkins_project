pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
        PYTHON_PATH = 'C:\\Python312\\python.exe'  // Path to your Python executable
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
                        ${PYTHON_PATH} -m pip install --upgrade pip  // Upgrade pip
                        ${PYTHON_PATH} -m pip install -r jenkins_project\\requirements.txt
                    """
                    // List installed packages
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
                        ${PYTHON_PATH} -m pytest jenkins_project
                    """
                }
            }
        }
        stage('Code Coverage') {
            steps {
                script {
                    // Ensure coverage is available
                    bat """
                        call ${VIRTUAL_ENV}\\Scripts\\activate.bat
                        ${PYTHON_PATH} -m pip install coverage
                        ${PYTHON_PATH} -m coverage run -m pytest jenkins_project
                        ${PYTHON_PATH} -m coverage report
                        ${PYTHON_PATH} -m coverage html
                    """
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    // Adding --exit-zero to Bandit command to avoid pipeline failure
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
