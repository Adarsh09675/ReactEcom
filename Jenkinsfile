pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@13.55.210.86"
        APP_PATH = "/var/www/react_app"
        NPM_CACHE = "$WORKSPACE/.npm_cache"          // Workspace-specific npm cache
        NODE_MODULES_CACHE = "$WORKSPACE/node_modules" // Cached node_modules
    }

    options {
        skipDefaultCheckout(true) // Avoid automatic checkout; we handle it manually
        timestamps()             // Log timestamps for easier debugging
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
                script {
                    echo "📦 Installing dependencies efficiently"

                    // If node_modules exists and package-lock.json is unchanged, skip full install
                    if (fileExists("${NODE_MODULES_CACHE}/package-lock.json") &&
                        sh(returnStatus: true, script: "cmp -s package-lock.json ${NODE_MODULES_CACHE}/package-lock.json") == 0) {
                        echo "✅ Using cached node_modules"
                        sh "cp -R $NODE_MODULES_CACHE ./node_modules"
                    } else {
                        echo "🚀 Installing from scratch using npm ci"
                        sh """
                            npm ci --cache $NPM_CACHE --jobs=2 --silent --no-progress
                            rm -rf $NODE_MODULES_CACHE
                            cp -R node_modules $NODE_MODULES_CACHE
                        """
                    }
                }
            }
        }

        stage('Build React App') {
            steps {
                echo "⚡ Building React app"
                sh "npm run build"
                // Verify build folder
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
                        ssh $DEPLOY_SERVER 'mkdir -p $APP_PATH'
                        rsync -avz --delete build/ $DEPLOY_SERVER:$APP_PATH
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
