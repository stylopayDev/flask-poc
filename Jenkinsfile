pipeline {
    agent any

    environment {
        DEPLOY_DIR = '/var/www/flaskapp'
        FLASK_CMD = "${HOME}/.local/bin/flask"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/stylopayDev/flask-poc.git'
            }
        }

        stage('Prepare Deploy Directory') {
            steps {
                sh '''
                    rm -rf $DEPLOY_DIR/*
                '''
            }
        }

        stage('Copy to Deploy Directory') {
            steps {
                sh '''
                    cp -r * $DEPLOY_DIR
                '''
            }
        }

        stage('Run Flask App') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    export FLASK_APP=app.py
                    export FLASK_ENV=production

                    pkill -f "flask run" || true

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
