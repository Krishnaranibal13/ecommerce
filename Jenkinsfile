
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Prepare Environment') {
            steps {
                echo 'Copying production environment file...'

                sh '''
                    if [ ! -f /home/ubuntu/ecommerce/.env ]; then
                        echo "ERROR: /home/ubuntu/ecommerce/.env not found"
                        exit 1
                    fi

                    cp /home/ubuntu/ecommerce/.env .env

                    echo ".env copied successfully"
                '''
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

                sh '''
                    docker compose ps

                    echo "Waiting for application..."
                    sleep 10

                    echo "Testing application..."
                    curl -f http://localhost/

                    echo "Application is working!"
                '''
            }
        }
    }

    post {
        success {
            echo '======================================'
            echo 'Ecommerce deployment successful!'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo 'Ecommerce deployment failed!'
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



