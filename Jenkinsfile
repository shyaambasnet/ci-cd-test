// pipeline {
//     agent any

//     environment {
//         DEPLOY_SERVER = "shyam@192.168.10.112"
//         DEPLOY_PATH = "/home/shyam/ci-cd-test"
//         SSH_CRED = "deploy-key"
//         PATH = "/usr/bin:/usr/local/bin:$PATH"
//         PORT = "3000" 
//     }

//     stages {

//         stage('Checkout') {
//             steps {
//                 git branch: 'main', url: 'https://github.com/shyaambasnet/ci-cd-test.git'
//             }
//         }

//         stage('Install Dependencies') {
//             steps {
//                 sh 'npm ci'
//             }
//         }

//         stage('Deploy') {
//             steps {
//                 sshagent([env.SSH_CRED]) {
//                     sh """
//                         rsync -avz --delete \
//                         --exclude='node_modules/' \
//                         -e "ssh -o StrictHostKeyChecking=no" \
//                         ./ $DEPLOY_SERVER:$DEPLOY_PATH

//                         ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER '
//                             cd $DEPLOY_PATH
//                             npm install --production

//                             pm2 describe ci-cd-test > /dev/null
//                             if [ \$? -eq 0 ]; then
//                                 pm2 restart ci-cd-test
//                             else
//                                 pm2 start index.js --name ci-cd-test
//                             fi
//                         '
//                     """
//                 }
//             }
//         }
//     }

//     post {
//         success {
//             echo "✅ Deployment successful!"
//         }
//         failure {
//             echo "❌ Deployment failed!"
//         }
//     }
// }
pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "shyam@192.168.10.112"
        DEPLOY_PATH = "/home/shyam/ci-cd-test"
        SSH_CRED = "deploy-key"
        PORT = "3000" 
        PATH = "/usr/bin:/usr/local/bin:$PATH"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "🔄 Checking out code..."
                git branch: 'main', url: 'https://github.com/shyaambasnet/ci-cd-test.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "📦 Installing dependencies..."
                sh 'npm ci'
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying to $DEPLOY_SERVER..."
                sshagent([env.SSH_CRED]) {
                    sh """
                        # Sync project files to remote server
                        rsync -avz --delete \
                            --exclude='node_modules/' \
                            -e "ssh -o StrictHostKeyChecking=no" \
                            ./ $DEPLOY_SERVER:$DEPLOY_PATH

                        # SSH into the server to deploy
                        ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER '
                            cd $DEPLOY_PATH
                            
                            # Set the PORT environment variable
                            export PORT=${PORT}

                            # Install production dependencies
                            npm install --production

                            # Start or restart app using PM2
                            pm2 describe ci-cd-test > /dev/null
                            if [ \$? -eq 0 ]; then
                                pm2 restart ci-cd-test
                            else
                                pm2 start index.js --name ci-cd-test --env production
                            fi
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo " Deployment successful! Your app should be running at http://192.168.10.112:${PORT}"
        }
        failure {
            echo " Deployment failed! Check Jenkins logs for errors."
        }
    }
}
