pipeline {
    agent any

    environment {
        EC2_USER = 'ec2-user'
        EC2_HOST = 'your-ec2-public-ip'
        SSH_KEY = credentials('ec2-ssh-key')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://your-repo-url.git'
            }
        }

        stage('Install Nginx on EC2') {
            steps {
                sshagent([SSH_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << EOF
                        sudo yum update -y
                        sudo yum install -y nginx
                        sudo systemctl start nginx
                        sudo systemctl enable nginx
                        EOF
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent([SSH_KEY]) {
                    sh """
                        scp -o StrictHostKeyChecking=no -r * ${EC2_USER}@${EC2_HOST}:/usr/share/nginx/html/
                    """
                }
            }
        }
    }
}
