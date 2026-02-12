pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "shyam@192.168.10.112"
        DEPLOY_PATH = "/home/shyam/ci-cd-test"
        SSH_CRED = "deploy-key"
        PATH = "/usr/bin:/usr/local/bin:$PATH"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shyaambasnet/ci-cd-test.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Deploy') {
            steps {
                sshagent([env.SSH_CRED]) {
                    sh """
                        rsync -avz --delete \
                        --exclude='node_modules/' \
                        -e "ssh -o StrictHostKeyChecking=no" \
                        ./ $DEPLOY_SERVER:$DEPLOY_PATH

                        ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER '
                            cd $DEPLOY_PATH
                            npm install --production

                            pm2 describe ci-cd-test > /dev/null
                            if [ \$? -eq 0 ]; then
                                pm2 restart ci-cd-test
                            else
                                pm2 start index.js --name ci-cd-test
                            fi
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful!"
        }
        failure {
            echo "❌ Deployment failed!"
        }
    }
}
