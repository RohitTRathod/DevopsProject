pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        DOCKER_CREDENTIALS_ID = credentials('jenkins-docker') // Replace with the ID of your Docker credentials
        SLACK_CHANNEL = '#internship'
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    }
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    // Pull and run the Docker container
                    sh 'docker pull rohittrathod/devops-project:latest'
                    sh 'docker run -d -p 3000:3000 rohittrathod/devops-project:latest'
                }
            }
        }
        stage('Slack notification') {
            steps {
                slackSend(channel: SLACK_CHANNEL, color: 'good', message: "Successfully Completed ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL})")
            }
        }
    }
    post {
        success {
            // Sending success message to Slack channel
            script {
                slackSend(channel: SLACK_CHANNEL, color: 'good', message: "Build succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
            }
        }
        failure {
            // Sending failure message to Slack channel
            script {
                slackSend(channel: SLACK_CHANNEL, color: 'danger', message: "Build failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
            }
        }
    }
}
