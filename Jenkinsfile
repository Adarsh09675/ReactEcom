pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@ 3.107.23.97"
        FRONTEND_PATH = "/var/www/react_app"
        BACKEND_PATH = "/var/www/node_app"
        APP_NAME = "my-node-backend"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Adarsh09675/ReactEcom.git'
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Prepare Backend') {
            steps {
                dir('backend') {
                    sh 'npm install'
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh """
                        # Deploy frontend
                        rsync -avz --delete frontend/build/ $DEPLOY_SERVER:$FRONTEND_PATH/

                        # Deploy backend
                        rsync -avz --delete backend/ $DEPLOY_SERVER:$BACKEND_PATH/

                        # Restart backend with PM2
                        ssh $DEPLOY_SERVER "
                            cd $BACKEND_PATH &&
                            npm install &&
                            pm2 start server.js --name $APP_NAME || pm2 restart $APP_NAME
                        "
                    """
                }
            }
        }
    }
}

