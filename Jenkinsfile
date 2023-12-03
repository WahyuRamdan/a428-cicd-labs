pipeline {
    agent {
        docker {
            image 'node:16.17.0'
            args "-p 3000:3000"
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'yarn install'
                sh 'yarn build'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [choice(choices: ['Yes'], description: 'Choose an option', name: 'CONTINUE')]
                    )
                    if (userInput == 'Yes') {
                        echo 'Continuing with deployment...'
                        // sh './jenkins/scripts/deliver.sh'
                        // sh 'sleep 60'
                        // sh './jenkins/scripts/kill.sh'
                    } else {
                        echo 'Deployment aborted.'
                        sh './jenkins/scripts/kill.sh'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Installing Cloudflare Wrangler'
                sh 'yarn add wrangler'
                echo 'Deploying to cloudflare pages'
                sh './jenkins/scripts/deliver.sh'
                sh 'sleep 60'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
