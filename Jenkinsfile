pipeline {
    agent any
    
    environment {
        NODE_VERSION = '18'  // Specify the Node.js version you're using
        PM2_NAME = 'social-media-backend'  // Name for your PM2 process
        SERVER_USER = 'ttr'
        SERVER_IP = '157.173.220.169'
        SERVER_PASSWORD = credentials('ttr-server-password')  // We'll create this in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Deploy to VM') {
            steps {
                script {
                    // Using sshpass to handle password-based authentication
                    sh """
                        sshpass -p "\${SERVER_PASSWORD}" ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} '
                        cd ~/Social-Media &&
                        git pull origin main &&
                        npm install &&
                        pm2 restart ${PM2_NAME} || pm2 start server.js --name ${PM2_NAME}
                        '
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}