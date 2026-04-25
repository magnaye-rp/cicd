pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    stages {
        stage("Checkout") {
            steps {
                git branch: "main", url: "https://github.com/YOUR_USERNAME/cicd.git"
            }
        }
        stage("Setup") {
            steps {
                sh "python3 -m venv venv && . venv/bin/activate && pip install -r requirements.txt"
            }
        }
        stage("Test") {
            steps {
                sh """
                . venv/bin/activate
                Xvfb :99 -screen 0 1024x768x16 &
                export DISPLAY=:99
                sleep 2
                python test.py
                """
            }
        }
        stage("Deploy") {
            steps {
                sh """
                sudo rsync -av --delete ./ /var/www/html/
                sudo chown -R www-data:www-data /var/www/html
                sudo chmod -R 755 /var/www/html
                """
            }
        }
    }
}
