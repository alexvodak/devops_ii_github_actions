pipeline {
    agent any

    environment {
        REMOTE_HOST = 'localhost'
        REMOTE_USER = 'user'
        SSH_KEY = credentials('ssh_key')
    }

    stages {
        stage('Install httpd on Remote Host') {
            steps {
                script {
                    sshagent(['SSH_KEY']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} << EOF
                        sudo apt-get update
                        sudo apt-get install -y apache2
                        EOF
                        """
                    }
                }
            }
        }
        stage('Check Logs for 4xx and 5xx Errors') {
            steps {
                script {
                    sshagent(['SSH_KEY']) {
                        sh """
                        ssh ${REMOTE_USER}@${REMOTE_HOST} << EOF
                        sudo cat /var/log/apache2/access.log | grep -E 'HTTP/1\\.[01]" [45][0-9]{2} '
                        EOF
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                sshagent(['SSH_KEY']) {
                    sh """
                    ssh ${REMOTE_USER}@${REMOTE_HOST} << EOF
                    sudo systemctl stop apache2
                    EOF
                    """
                }
            }
        }
    }
}
