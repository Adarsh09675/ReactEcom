pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@3.107.23.97"
        APP_PATH = "/var/www/react_app"
    }

    stages {
        stage('Build Frontend') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh """
                        # Deploy build files to deployment server
                        rsync -avz --delete build/ $DEPLOY_SERVER:$APP_PATH/
                    """
                }
            }
        }
    }
}
