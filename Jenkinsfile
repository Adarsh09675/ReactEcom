pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "ubuntu@13.210.217.171"
        APP_DIR = "/home/ubuntu/react-app"
        NGINX_DIR = "/var/www/html"
        AWS_BUCKET = "my-react-app-bucket"
        REGION = "ap-southeast-2"
    }

    stages {
        stage('Clone Repo') {
            steps {
                echo "📥 Cloning GitHub repository..."
                git branch: 'main', url: 'https://github.com/Adarsh09675/ReactEcom.git'
            }
        }

        stage('Install & Build') {
            steps {
                echo "⚙️ Installing dependencies..."
                sh 'npm install'
                echo "🏗 Building React app..."
                sh 'npm run build'
            }
        }

        stage('Deploy to EC2 + Nginx') {
            steps {
                sshagent(['deploy-key']) {
                    sh '''
                    echo "🚀 Syncing project to deployment server..."
                    rsync -avz -e "ssh -o StrictHostKeyChecking=no" --exclude node_modules --exclude .git ./ $DEPLOY_SERVER:$APP_DIR

                    ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER << 'EOF'
                    set -e
                    cd ~/react-app
                    if [ ! -d "node_modules" ] || [ package.json -nt node_modules/.package_stamp ]; then
                        echo "Installing npm dependencies..."
                        npm install
                        touch node_modules/.package_stamp
                    else
                        echo "Dependencies already installed, skipping npm install"
                    fi
                    echo "Building React app..."
                    npm run build
                    echo "Deploying to Nginx directory..."
                    sudo rm -rf /var/www/html/*
                    sudo cp -r build/* /var/www/html/
                    echo "✅ Deployment to EC2 + Nginx completed!"
                    EOF
                    '''
                }
            }
        }

        stage('Deploy to S3') {
            steps {
                echo "☁️ Deploying build to S3..."
                withAWS(region: "${REGION}", credentials: "aws-credentials") {
                    sh '''
                        aws s3 sync build/ s3://$AWS_BUCKET --delete --region $REGION
                        echo "✅ Deployment to S3 completed!"
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
