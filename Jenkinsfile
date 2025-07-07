pipeline {
    agent any

    environment {
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
                    // SSH to the server and set up Python environment
                    sh '''
                    ssh username@remote-server "
                    cd $DEPLOY_DIR
                    python3 -m venv venv  # Create virtual environment if not exists
                    source venv/bin/activate  # Activate virtual environment
                    pip install -r requirements.txt  # Install dependencies
                    "
                    '''
                }
            }
        }

        
        stage('Deploy Application') {
            steps {
                script {
                    // SSH to the server and deploy the application
                    sh '''
                    scp -r * username@remote-server:$DEPLOY_DIR
                    ssh username@remote-server "
                    cd $DEPLOY_DIR
                    source venv/bin/activate  # Activate virtual environment
                    export FLASK_APP=app.py
                    export FLASK_ENV=production  # Or 'development' if you prefer
                    flask run --host=0.0.0.0 --port=5000 &  # Run Flask in the background
                    "
                    '''
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    // Optional cleanup after deployment
                    echo "Cleaning up after deployment"
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
