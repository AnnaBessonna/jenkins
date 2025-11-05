pipeline {
    agent any

    stages {
        stage('Update system') {
            steps {
                echo 'Updating system packages...'
                sh 'sudo apt update -y'
            }
        }

        stage('Install Apache2') {
            steps {
                echo 'Installing Apache2...'
                sh '''
                if ! dpkg -s apache2 >/dev/null 2>&1; then
                    sudo apt install apache2 -y
                else
                    echo "Apache2 is already installed."
                fi
                '''
            }
        }

        stage('Start Apache2') {
            steps {
                echo 'Starting Apache2 service...'
                sh '''
                sudo systemctl enable apache2
                sudo systemctl start apache2
                sudo systemctl status apache2 | grep active
                '''
            }
        }

        stage('Verify Apache2') {
            steps {
                echo 'Checking Apache2 response...'
                sh 'curl -I http://localhost | head -n 1'
            }
        }
    }
        stage('Check Apache Logs') {
            steps {
                echo '🔍 Checking Apache logs for 4xx and 5xx errors...'
                sh '''
                LOG_FILE="/var/log/apache2/access.log"

                if [ -f "$LOG_FILE" ]; then
                    echo "Reading Apache access log..."
                    sudo grep "HTTP/1.[01]\" [45][0-9][0-9]" "$LOG_FILE" > errors.log || true

                    if [ -s errors.log ]; then
                        echo "Found the following errors in Apache log:"
                        cat errors.log
                        exit 1
                    else
                        echo "No 4xx or 5xx errors found in Apache logs."
                    fi
                else
                    echo "⚠️ Apache log file not found at $LOG_FILE"
                fi
                '''
            }
        }

    post {
        success {
            echo 'Apache2 successfully installed and running!'
        }
        failure {
            echo 'Pipeline failed. Check Jenkins logs.'
        }
    }
}
