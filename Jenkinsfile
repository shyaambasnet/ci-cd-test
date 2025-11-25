pipeline {
    agent any

    environment {
        REMOTE_USER = "shyam"
        REMOTE_HOST = "192.168.10.107"
        APP_DIR = "/home/shyam/ci-cd-test"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/shyaambasnet/ci-cd-test.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Deploy to Server') {
            steps {
                // Copy code to remote server
                sh "rsync -avz --delete ./ ${REMOTE_USER}@${REMOTE_HOST}:${APP_DIR}/"

                // SSH and start/restart app using PM2
                sh """
                ssh ${REMOTE_USER}@${REMOTE_HOST} << EOF
                cd ${APP_DIR}
                npm install
                pm2 startOrRestart ecosystem.config.js --env production
                pm2 save
                EOF
                """
            }
        }
    }
}

