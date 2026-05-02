pipeline {
    agent any
    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        DEPLOY = "/var/www/html"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Evina-Suanes24/CICD.git',
                    credentialsId: 'github-pat'
            }
        }
        stage('Setup Python') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                sudo rsync -av --delete ./ ${DEPLOY}/
                sudo chown -R www-data:www-data ${DEPLOY}
                sudo chmod -R 755 ${DEPLOY}
                '''
            }
        }
        stage('Start Apache') {
            steps {
                sh 'sudo systemctl restart apache2'
            }
        }
        stage('Test') {
            steps {
                sh '''
                . venv/bin/activate
                pkill Xvfb || true
                rm -f /tmp/.X99-lock
                Xvfb :99 -screen 0 1024x768x16 &
                export DISPLAY=:99
                sleep 3
                python test.py
                '''
            }
        }
    }
}
