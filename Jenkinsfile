pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'
        FLASK_CMD = 'flask'
    }

    stages {
        stage('System Setup') {
            steps {
                sh '''
                    # Ensure non-interactive apt
                    export DEBIAN_FRONTEND=noninteractive

                    # Update system and install necessary packages
                    sudo apt-get update -y
                    sudo apt-get install -y python3 python3-pip python3-venv

                    # Upgrade pip
                    sudo pip3 install --upgrade pip
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/stylopayDev/flask-poc.git'
            }
        }

        stage('Prepare Deploy Directory') {
            steps {
                sh '''
                    # Clean out existing files inside deploy dir (but not the folder itself)
                    sudo rm -rf $DEPLOY_DIR/*
                '''
            }
        }

        stage('Copy to Deploy Directory') {
            steps {
                sh '''
                    # Copy everything including hidden files (e.g. .flaskenv, .env)
                    sudo cp -a . $DEPLOY_DIR
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    if [ -f requirements.txt ]; then
                        sudo pip3 install -r requirements.txt
                    else
                        echo "ERROR: requirements.txt not found in $DEPLOY_DIR"
                        exit 1
                    fi
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    export FLASK_APP=app.py
                    export FLASK_ENV=production

                    # Kill any previously running instance
                    pkill -f "flask run" || true

                    # Run Flask in the background
                    nohup $FLASK_CMD run --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Flask App Deployed Successfully!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}
