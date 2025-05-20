
pipeline {
     agent {
        docker {
            image 'python:3.11'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                sh 'python3 -m pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Lint') {
            steps {
                sh 'flake8 app/ tests/'
            }
        }

        stage('Test') {
            steps {
                sh 'python3 -m pytest --cov=app tests/'
            }
            post {
                always {
                    sh 'python3 -m pytest --cov=app --cov-report=xml tests/'
                    junit 'pytest-results.xml'// Requires pytest-junit plugin
                }
            }
        }

        stage('Build') {
            steps {
                sh 'pip install wheel'
                sh 'python3 setup.py bdist_wheel'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'dist/*.whl', fingerprint: true
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging environment...'
                sh 'mkdir -p staging && cp dist/*.whl staging/'
            }
        }
    }
}
