pipeline {
    agent any

    stages {
        stage('Update system') {
            steps {
                sh 'sudo apt update -y'
            }
        }

        stage('Install Apache2') {
            steps {
                sh 'sudo apt install -y apache2'
            }
        }

        stage('Start Apache2') {
            steps {
                sh '''
                sudo systemctl enable apache2
                sudo systemctl start apache2
                '''
            }
        }

        stage('Check Apache Response') {
            steps {
                sh '''
                RESPONSE=$(curl -I http://localhost 2>/dev/null | head -n 1)
                echo "Response: $RESPONSE"
                if echo "$RESPONSE" | grep -q "200 OK"; then
                    echo "Apache2 is working!"
                else
                    echo "Apache2 failed to respond correctly."
                    exit 1
                fi
                '''
            }
        }

        stage('Check Apache Logs') {
            steps {
                sh '''
                LOG_FILE="/var/log/apache2/access.log"
                if [ -f "$LOG_FILE" ]; then
                    sudo grep 'HTTP/1.[01]" [45][0-9][0-9]' "$LOG_FILE" > errors.log || true
                    if [ -s errors.log ]; then
                        echo "Found errors in Apache logs:"
                        cat errors.log
                        exit 1
                    else
                        echo "No 4xx or 5xx errors found."
                    fi
                else
                    echo "Apache log file not found."
                fi
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Apache installed, verified and logs checked!'
        }
        failure {
            echo 'FAILURE: See console output for details.'
        }
    }
}
