pipeline {
    agent any

    environment {
        // Налаштування цільової машини
        REMOTE_USER = 'dev'
        REMOTE_HOST = '192.168.0.104'
        TARGET_NAME = 'dev-server'
    }

    stages {
        stage('Deploy Apache') {
            steps {
                echo "Deploying to ${TARGET_NAME} server..."
                sh """
                ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "
                    sudo apt update -y
                    sudo apt install apache2 -y
                    sudo systemctl enable apache2
                    sudo systemctl start apache2
                "
                """
            }
        }

        stage('Verify Service') {
            steps {
                echo "Checking Apache status on ${TARGET_NAME}..."
                sh """
                ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} "
                    systemctl is-active apache2
                    curl -I localhost
                "
                """
            }
        }

        stage('Log Analysis 4xx/5xx') {
            steps {
                echo "Analyzing logs on ${TARGET_NAME}..."
                sh """
                ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} '
                    echo "----- 4xx Errors -----"
                    grep " 4[0-9][0-9] " /var/log/apache2/access.log | tail -5 || true

                    echo "----- 5xx Errors -----"
                    grep " 5[0-9][0-9] " /var/log/apache2/access.log | tail -5 || true
                '
                """
            }
        }
    }

    post {
        success {
            echo "Deployment to ${TARGET_NAME} successful!"
        }
        failure {
            echo "Deployment to ${TARGET_NAME} failed. Check network or SSH keys."
        }
    }
}
