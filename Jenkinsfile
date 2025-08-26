pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@3.106.166.97"
        APP_PATH = "/var/www/react_app"
        NPM_CACHE = "~/.npm"  // Use npm cache
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:Adarsh09675/ReactEcom.git',
                    credentialsId: 'Ecom-credential'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh """
                    echo "Using npm cache at $NPM_CACHE"
                    npm ci --cache $NPM_CACHE
                """
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to Server') {
            steps {
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
