
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker images...'
                sh 'docker compose build'
            }
        }

        stage('Stop Existing Containers') {
            steps {
                echo 'Stopping existing containers...'
                sh 'docker compose down'
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Starting application containers...'
                sh 'docker compose up -d'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Checking containers...'
                sh 'docker compose ps'

                echo 'Checking application through Nginx...'
                sh '''
                    sleep 10
                    curl -f http://localhost/
                '''
            }
        }
    }

    post {
        success {
            echo '======================================'
            echo 'Ecommerce deployment successful!'
            echo 'Application is running on port 80.'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo 'Ecommerce deployment failed!'
            echo 'Check Jenkins console output and container logs.'
            echo '======================================'

            sh '''
                docker compose ps || true
                docker compose logs --tail=50 || true
            '''
        }

        always {
            echo 'Pipeline completed.'
        }
    }
}

