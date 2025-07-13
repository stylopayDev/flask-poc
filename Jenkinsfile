pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'
        VENV_DIR = "${DEPLOY_DIR}/venv"
    }

    stages {
        stage('System Setup') {
            steps {
                sh '''
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
                sh "sudo rm -rf ${DEPLOY_DIR}/*"
            }
        }

        stage('Copy to Deploy Directory') {
            steps {
                sh "sudo cp -a . ${DEPLOY_DIR}"
            }
        }

        stage('Setup Virtualenv and Install Dependencies') {
            steps {
                sh '''
                    cd $DEPLOY_DIR
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                sh '''
                    cd $DEPLOY_DIR
                    touch flask.log
                    chmod 666 flask.log

                    # Kill existing app.py process if any
                    pkill -f "/var/www/flaskapp/venv/bin/python app.py" || true
                    sleep 10
                    . venv/bin/activate
                    # nohup /var/www/flaskapp/venv/bin/python app.py > flask.log 2>&1 &
                    FLASK_APP=app.py flask run --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
                    sleep 10
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
