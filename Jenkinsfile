pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'  // Path to your Flask app directory
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
                    // Set up the Python virtual environment and install dependencies locally
                    sh '''
                    if [ ! -d "$DEPLOY_DIR/venv" ]; then
                        python3 -m venv $DEPLOY_DIR/venv  # Create virtual environment if not exists
                    fi
                    source $DEPLOY_DIR/venv/bin/activate  # Activate virtual environment
                    pip install -r $DEPLOY_DIR/requirements.txt  # Install dependencies
                    '''
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    // Optional: Run tests before deploying
                    sh '''
                    source $DEPLOY_DIR/venv/bin/activate
                    pytest $DEPLOY_DIR/tests/  # Run tests if you have any
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    // Deploy by copying the files to the deploy directory
                    // Since Jenkins is on the same server, no need for SCP or SSH
                    sh '''
                    cp -r * $DEPLOY_DIR
                    cd $DEPLOY_DIR
                    source venv/bin/activate  # Activate virtual environment
                    export FLASK_APP=app.py
                    export FLASK_ENV=production  # Or 'development' if you prefer
                    nohup flask run --host=0.0.0.0 --port=5000 &> flask.log &  # Run Flask in the background
                    '''
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    // Optional cleanup after deployment
                    echo "Cleaning up after deployment"
                    // You could remove temporary files, clear logs, etc.
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
