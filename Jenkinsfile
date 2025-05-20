
pipeline {
    agent any

    environment{
         VENV = 'venv'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv $VENV
                    . $VENV/bin/activate
                    pip install --upgrade pip -
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    . venv/bin/activate
                    flake8 app/ tests/
                '''
            }
        }


        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    python3 -m pytest --cov=app --cov-report=xml --junitxml=pytest-results.xml tests/
                '''
            }
            post {
                always {
                    junit 'pytest-results.xml'
                }
            }
        }


        stage('Build') {
            steps {
                sh '''
                    . venv/bin/activate
                    pip install wheel
                    python3 setup.py bdist_wheel
                '''
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
