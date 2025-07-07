pipeline {
    agent any

    environment {
        // Define environment variables if needed
        DEPLOY_DIR = '/var/www/flaskapp'
        APP_NAME = 'flaskapp'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull the latest code from GitHub
                git 'https://github.com/stylopayDev/flask-poc.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    // Install Python and required packages
                    sh 'python3 -m venv venv'
                    sh 'source venv/bin/activate && pip install -r requirements.txt'
                }
            }
        }
        
        stage('Test Application') {
            steps {
                script {
                    // Run your tests if you have any
                    sh 'pytest tests/'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build a Docker image if you're using Docker
                    sh 'docker build -t $APP_NAME .'
                }
            }
        }
        
        stage('Deploy Application') {
            steps {
                script {
                    // Here we can either deploy to a remote server or use Docker to run the app
                    // For remote server, use SSH to copy files and restart the app
                    sh '''
                    scp -r * username@remote-server:$DEPLOY_DIR
                    ssh username@remote-server 'cd $DEPLOY_DIR && source venv/bin/activate && flask run'
                    '''
                    
                    // Alternatively, if using Docker:
                    sh 'docker run -d -p 5000:5000 $APP_NAME'
                }
            }
        }
        
        stage('Clean up') {
            steps {
                script {
                    // Optional cleanup after deployment
                    sh 'docker rmi $APP_NAME'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
