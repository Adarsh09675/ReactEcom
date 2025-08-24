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
                sshagent(['deploy-key']) {
                    sh '''
                    # Copy project source code to deployment server
                    rsync -avz --exclude node_modules ./ ubuntu@<Deployment-Server-IP>:/home/ubuntu/react-app/

                    # Build React app on deployment server
                    ssh ubuntu@<Deployment-Server-IP> << 'EOF'
                      cd ~/react-app
                      npm install
                      npm run build
                      sudo rm -rf /var/www/html/*
                      sudo cp -r build/* /var/www/html/
                    EOF
                    '''
                }
            }
        }
    }
}
