pipeline {
    agent any

    environment {
        APP_NAME = "node-cicd-demo"
        APP_DIR = "/opt/node-cicd-demo"
        APP_PORT = "3000"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Nageshwarrao9/devops-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --runInBand'
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Building Node.js application..."'
                sh 'npm pack'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    sudo mkdir -p ${APP_DIR}
                    sudo rm -rf ${APP_DIR}/*
                    sudo cp -r . ${APP_DIR}/
                    cd ${APP_DIR}
                    sudo npm ci --omit=dev
                '''
            }
        }

        stage('Restart Application') {
            steps {
                sh '''
                    sudo systemctl restart ${APP_NAME}
                    sudo systemctl status ${APP_NAME} --no-pager
                '''
            }
        }

        stage('Smoke Test') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://localhost:${APP_PORT}/health
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}