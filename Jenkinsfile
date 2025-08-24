pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@52.62.158.15" // Deployment EC2 IP
        APP_DIR = "/home/ubuntu/react-app"
        NGINX_DIR = "/var/www/html"
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo "📥 Cloning GitHub repository..."
                git branch: 'main', url: 'https://github.com/Adarsh09675/ReactEcom.git'
            }
        }

        stage('Sync & Deploy') {
            steps {
                sshagent(['deploy-key']) {
                    sh '''
                    echo "🚀 Syncing project to deployment server..."
                    rsync -avz -e "ssh -o StrictHostKeyChecking=no" --exclude node_modules --exclude .git ./ $DEPLOY_SERVER:$APP_DIR

                    echo "📦 Building React app on deployment server..."
                    ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER "
                        set -e
                        cd ~/react-app

                        # Only install dependencies if package.json changed
                        if [ ! -d 'node_modules' ] || [ package.json -nt node_modules/.package_stamp ]; then
                            echo '🔧 Installing npm dependencies...'
                            npm install
                            mkdir -p node_modules
                            touch node_modules/.package_stamp
                        else
                            echo '✅ Dependencies already installed, skipping npm install'
                        fi

                        echo '🏗️ Running build...'
                        npm run build

                        echo '📂 Deploying to Nginx directory...'
                        sudo rm -rf $NGINX_DIR/*
                        sudo cp -r build/* $NGINX_DIR/

                        echo '✅ Deployment completed!'
                    "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs!"
        }
    }
}
