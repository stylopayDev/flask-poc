pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'
        VENV_DIR = "${DEPLOY_DIR}/venv"
        GUNICORN_CMD = "${VENV_DIR}/bin/gunicorn"
    }

    stages {
        stage('System Setup') {
            steps {
                sh '''
                    export DEBIAN_FRONTEND=noninteractive
                    sudo apt-get update -y
                    sudo apt-get install -y python3 python3-pip python3-venv
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
                    sudo rm -rf $DEPLOY_DIR/*
                '''
            }
        }

        stage('Copy to Deploy Directory') {
            steps {
                sh '''
                    sudo cp -a . $DEPLOY_DIR
                '''
            }
        }

        stage('Install Dependencies in Venv') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    python3 -m venv venv
                    . venv/bin/activate

                    pip install --upgrade pip

                    # Install your requirements plus gunicorn
                    pip install -r requirements.txt
                    pip install gunicorn
                '''
            }
        }

        stage('Run Flask App with Gunicorn') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    pkill -f "gunicorn" || true

                    . venv/bin/activate

                    nohup $GUNICORN_CMD -w 4 -b 0.0.0.0:5000 app:app > flask.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Flask App Deployed and Running with Gunicorn!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}
