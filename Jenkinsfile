pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AnnaBessonna/jenkins'
            }
        }

        stage('Update System') {
            steps {
                echo 'Updating packages...'
                sh 'sudo apt update -y'
            }
        }

        stage('Install Apache2') {
            steps {
                echo 'Installing Apache2...'
                sh 'sudo apt install -y apache2'
            }
        }

        stage('Start Apache2') {
            steps {
                echo 'Starting Apache2...'
                sh '''
                    sudo systemctl enable apache2
                    sudo systemctl start apache2
                    sudo systemctl status apache2 | grep active
                '''
            }
        }

        stage('Deployment Verification') {
            steps {
                echo 'Verifying deployment...'
                sh '''
                    RESPONSE=$(curl -I http://localhost | head -n 1)
                    echo "$RESPONSE"
                    if echo "$RESPONSE" | grep -q "200 OK"; then
                        echo "Deployment successful!"
                    else
                        echo "Deployment failed!"
                        exit 1
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline finished successfully!'
        }
        failure {
            echo '❗ Pipeline failed. Check Jenkins logs.'
        }
    }
}
