pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Train Model') {
            steps {
                sh '''
                . venv/bin/activate
                python scripts/train.py
                '''
            }
        }

        stage('Read Metrics') {
            steps {
                sh '''
                . venv/bin/activate
                echo "===== MODEL METRICS ====="
                echo "2022BCS0106 - Pasam Sai Teja"
                python -c "
import json
m=json.load(open('metrics.json'))
print('R2:',m['r2'])
print('RMSE:',m['rmse'])
"
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t 2022bcs0106saiteja/wine-inference:latest .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    docker push <your-dockerhub-username>/wine-inference:latest
                    '''
                }
            }
        }
    }
}
