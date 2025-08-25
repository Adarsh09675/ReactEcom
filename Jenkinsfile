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
                    echo "🚀 Copying build folder to deployment server..."
                    rsync -avz -e "ssh -o StrictHostKeyChecking=no" build/ $DEPLOY_SERVER:$APP_DIR/build

                    ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER << 'EOF'
                    set -e
                    echo "Deploying build folder to Nginx directory..."
                    sudo rm -rf /var/www/html/*
                    sudo cp -r $APP_DIR/build/* /var/www/html/
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
