pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@3.106.166.97"
        APP_PATH = "/var/www/react_app"
    }

    stages {
        stage('Checkout') {
            steps {
                // Explicitly specify the main branch
                git branch: 'main', 
                    url: 'git@github.com:Adarsh09675/ReactEcom.git', 
                    credentialsId: 'Ecom-credential'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to Server') {
            steps {
                // Ensure the deployment folder exists
                sh """
                    ssh $DEPLOY_SERVER 'mkdir -p $APP_PATH'
                    rsync -avz --delete build/ $DEPLOY_SERVER:$APP_PATH
                """
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
