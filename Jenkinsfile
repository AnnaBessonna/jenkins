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

    post {
        success {
            echo 'Apache2 successfully installed and running!'
        }
        failure {
            echo 'Pipeline failed. Check Jenkins logs.'
        }
    }
}
