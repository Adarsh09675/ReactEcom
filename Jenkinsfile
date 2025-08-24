pipeline {
    agent any

    environment {
        DEPLOY_SERVER = 'ubuntu@52.62.158.15'
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
                    sh """
                        echo "🚀 Syncing project to deployment server..."
                        rsync -avz -e "ssh -o StrictHostKeyChecking=no" --exclude node_modules --exclude .git ./ \$DEPLOY_SERVER:/home/ubuntu/react-app

                        echo "📦 Building React app on deployment server..."
                        ssh -o StrictHostKeyChecking=no \$DEPLOY_SERVER "
                            cd ~/react-app
                            npm install
                            npm run build
                            sudo rm -rf /var/www/html/*
                            sudo cp -r build/* /var/www/html/
                        "
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs!"
        }
    }
}

