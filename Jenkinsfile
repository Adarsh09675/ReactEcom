pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@3.25.169.192"
        APP_PATH = "/var/www/react_app"
        NPM_CACHE = "$WORKSPACE/.npm_cache"
    }

    options {
        skipDefaultCheckout(true)
        timestamps()
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
                echo "📦 Installing dependencies efficiently"
                sh """
                    mkdir -p $NPM_CACHE
                    npm ci --cache $NPM_CACHE --prefer-offline --jobs=$(nproc) --silent --no-progress
                """
            }
        }

        stage('Build React App') {
            steps {
                echo "⚡ Building React app"
                sh "npm run build"
                script {
                    if (!fileExists("build/index.html")) {
                        error "❌ Build failed! build/index.html not found."
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sshagent(['DEPLOY_SERVER_SSH']) {
                    sh """
                        rsync -az --delete build/ $DEPLOY_SERVER:$APP_PATH
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

