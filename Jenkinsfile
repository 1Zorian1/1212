pipeline {
    agent any

    environment {
        REMOTE_HOST = "192.168.0.107"
        REMOTE_USER = "ubuntu"
    }

    stages {

        stage('Deploy Apache') {
            steps {
                sh """
                ssh ${REMOTE_USER}@${REMOTE_HOST} '
                    sudo apt update -y &&
                    sudo apt install apache2 -y &&
                    sudo systemctl enable apache2 &&
                    sudo systemctl start apache2
                '
                """
            }
        }

        stage('Verify Service') {
            steps {
                sh """
                ssh ${REMOTE_USER}@${REMOTE_HOST} 'systemctl status apache2 --no-pager'
                """
            }
        }

        stage('Log Analysis 4xx/5xx') {
            steps {
                sh """
                ssh ${REMOTE_USER}@${REMOTE_HOST} '
                    echo "4xx errors:" &&
                    grep " 4[0-9][0-9] " /var/log/apache2/access.log | tail -5 || true &&
                    echo "5xx errors:" &&
                    grep " 5[0-9][0-9] " /var/log/apache2/access.log | tail -5 || true
                '
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
