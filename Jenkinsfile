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

        stage('Copy to Deploy Directory') {
            steps {
                sh '''
                    mkdir -p $DEPLOY_DIR
                    cp -r * $DEPLOY_DIR
                '''
            }
        }

        stage('Set Up Virtualenv & Install Dependencies') {
            steps {
                sh '''
                    cd $DEPLOY_DIR

                    if [ ! -d "venv" ]; then
                        python3 -m venv venv
                    fi

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
                    . venv/bin/activate

                    export FLASK_APP=app.py
                    export FLASK_ENV=production

                    # Kill any previously running Flask instance
                    pkill -f "flask run" || true

                    # Run Flask in background
                    nohup flask run --host=0.0.0.0 --port=5000 > flask.log 2>&1 &
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
