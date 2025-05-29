pipeline {
    agent any

    environment {
        BLUE_PATH = '/var/www/blue'
        GREEN_PATH = '/var/www/green'
        LIVE_LINK = '/var/www/live'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Akumar-86/blue-green-deploy.git'
            }
        }

        stage('Determine Current Live Environment') {
            steps {
                script {
                    def currentLink = sh(script: "readlink -f ${env.LIVE_LINK}", returnStdout: true).trim()
                    env.CURRENT_ENV = currentLink.contains('blue') ? 'blue' : 'green'
                    env.TARGET_ENV = env.CURRENT_ENV == 'blue' ? 'green' : 'blue'
                    echo "Current Live Environment: ${env.CURRENT_ENV}"
                    echo "Target Deployment Environment: ${env.TARGET_ENV}"
                }
            }
        }

        stage('Deploy to Target Environment') {
            steps {
                script {
                    def targetPath = env.TARGET_ENV == 'blue' ? env.BLUE_PATH : env.GREEN_PATH
                    sh "rm -rf ${targetPath}/*"
                    sh "cp -r * ${targetPath}/"
                    echo "Deployed to ${targetPath}"
                }
            }
        }

        stage('Switch Live Environment') {
            steps {
                script {
                    def targetPath = env.TARGET_ENV == 'blue' ? env.BLUE_PATH : env.GREEN_PATH
                    sh "ln -sfn ${targetPath} ${env.LIVE_LINK}"
                    echo "Switched live environment to ${env.TARGET_ENV}"
                }
            }
        }
    }
}
