pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'
        VENV_DIR = '/var/www/flaskapp/venv'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/stylopayDev/flask-poc.git'
            }
        }

        stage('Prepare Environment') {
            steps {
                sh '''
                    #!/bin/bash
                    set -e  # fail on any error

                    echo "[*] Creating deploy directory if it doesn't exist..."
                    mkdir -p $DEPLOY_DIR

                    echo "[*] Copying project files..."
                    cp -r * $DEPLOY_DIR
                '''
            }
        }

        stage('Setup Virtualenv & Install Dependencies') {
            steps {
                sh '''
                    #!/bin/bash
                    set -e

                    echo "[*] Checking if venv exists..."
                    if [ ! -d "$VENV_DIR" ]; then
                        echo "[*] Creating virtual environment..."
                        python3 -m venv $VENV_DIR
                    fi

                    echo "[*] Activating virtual environment and installing dependencies..."
                    source $VENV_DIR/bin/activate
                    pip install --upgrade pip
                    pip install -r $DEPLOY_DIR/requirements.txt
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                sh '''
                    #!/bin/bash
                    set -e

                    echo "[*] Starting Flask app..."
                    cd $DEPLOY_DIR
                    source venv/bin/activate
                    export FLASK_APP=app.py
                    export FLASK_ENV=production

                    # Kill previous flask if any (naive but works for POC)
                    pkill -f "flask run" || true

                    nohup flask run --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}
