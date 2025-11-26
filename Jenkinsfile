pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh '''bash -lc "
                python3 -m venv venv || python -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                "'''
            }
        }
        stage('Test') {
            steps {
                echo 'Running unit tests in venv...'
                sh '''bash -lc "
                . venv/bin/activate
                python -m unittest discover -s .
                "'''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application to mock directory...'
                sh '''
                mkdir -p /python-app-deploy
                cp /app.py /python-app-deploy/
                cp /requirements.txt /python-app-deploy/
                '''
            }
        }
        stage('Run Application') {
            steps {
                echo 'Running application in background (nohup) ...'
                sh '''
                nohup bash -lc ". venv/bin/activate && python /python-app-deploy/app.py" > /python-app-deploy/app.log 2>&1 &
                echo $! > /python-app-deploy/app.pid
                '''
            }
        }
        stage('Test Application') {
            steps {
                echo 'Testing the running application using unit test (again) ...'
                sh '''bash -lc "
                . venv/bin/activate
                python /test_app.py
                "'''
            }
        }
    }
    post {
        success { echo 'Pipeline completed successfully!' }
        failure { echo 'Pipeline failed. Check the logs for more details.' }
    }
}
