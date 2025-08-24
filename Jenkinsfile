pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Adarsh09675/ReactEcom.git'
            }
        }

        stage('Deploy & Build on Server') {
            steps {
                sshagent(['deploy-key']) {   // make sure this ID matches Jenkins credentials
                    sh '''
                    DEPLOY_SERVER=ubuntu@52.62.158.15   # replace with your EC2 public IP

                    echo "🚀 Syncing project to deployment server..."
                    rsync -avz --exclude node_modules --exclude .git ./ $DEPLOY_SERVER:/home/ubuntu/react-app/

                    echo "📦 Building React app on server..."
                    ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER << 'EOF'
                      cd ~/react-app
                      npm install
                      npm run build
                      sudo rm -rf /var/www/html/*
                      sudo cp -r build/* /var/www/html/
                      echo "✅ Deployment completed!"
                    EOF
                    '''
                }
            }
        }
    }
}

