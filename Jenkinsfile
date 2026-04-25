pipeline {
    agent any
    environment {
        GIT_REPO = 'https://github.com/magnaye-rp/cicd.git'
        BRANCH = 'main'
        DEPLOY_PATH = '/var/www/html'
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }
        stage('Setup Python') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }
        stage('Start Apache') {
            steps {
                sh 'sudo systemctl start apache2'
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                
                # Kill any leftover processes
                pkill -f Xvfb || true
                pkill -f chromedriver || true
                sleep 1
                
                # Start fresh Xvfb
                Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
                sleep 3
                
                # Run test with display set
                export DISPLAY=:99
                python test.py
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                sudo rsync -av --delete ./ ${DEPLOY_PATH}/
                sudo chown -R www-data:www-data ${DEPLOY_PATH}
                sudo chmod -R 755 ${DEPLOY_PATH}
                '''
            }
        }
    }
}
