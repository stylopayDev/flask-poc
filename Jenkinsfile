pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'
        VENV_DIR = "${DEPLOY_DIR}/venv"
        FLASK_CMD = "${VENV_DIR}/bin/flask"
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

                    # Create venv and activate
                    python3 -m venv venv
                    . venv/bin/activate

                    # Upgrade pip and install specific versions
                    pip install --upgrade pip

                    # Install requirements (make sure requirements.txt is fixed!)
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    export FLASK_APP=app.py
                    export FLASK_ENV=production

                    # Kill old instance
                    pkill -f "flask run" || true

                    # Run the app using venv flask
                    nohup $FLASK_CMD run --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Flask App Deployed and Running!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}
