pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@13.239.25.175"
        APP_PATH = "/var/www/react_app"
        NPM_CACHE = "$WORKSPACE/.npm_cache"   // Workspace-specific npm cache for faster builds
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:Adarsh09675/ReactEcom.git',
                    credentialsId: 'Ecom-credential'
            }
        }

        stage('Install Dependencies & Build') {
            steps {
                sh """
                    echo "Using npm cache at $NPM_CACHE"
                    npm ci --cache $NPM_CACHE
                    npm run build
                """
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(['DEPLOY_SERVER_SSH']) {
                    sh """
                        # Create deployment directory if not exists
                        ssh $DEPLOY_SERVER 'mkdir -p $APP_PATH'

                        # Sync build files to deployment server
                        rsync -avz --delete build/ $DEPLOY_SERVER:$APP_PATH

                        # Restart Nginx to serve the new build
                        ssh $DEPLOY_SERVER 'sudo systemctl restart nginx'
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
