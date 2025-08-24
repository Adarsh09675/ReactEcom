pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                // Clone your GitHub repository
                git branch: 'main', url: 'https://github.com/Adarsh09675/ReactEcom.git'
            }
        }

        stage('Deploy & Build on Server') {
            steps {
                // Use the SSH credentials added in Jenkins (ID: deploy-key)
                sshagent(['deploy-key']) {
                    sh '''
                    # Set Deployment server variable
                    DEPLOY_SERVER=ubuntu@52.62.158.15   # Replace with your deployment EC2 IP

                    echo "🚀 Syncing project to deployment server..."
                    # Rsync with SSH option to skip host key verification
                    rsync -avz -e "ssh -o StrictHostKeyChecking=no" --exclude node_modules --exclude .git ./ $DEPLOY_SERVER:/home/ubuntu/react-app/

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
