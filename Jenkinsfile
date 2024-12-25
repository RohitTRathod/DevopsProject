pipeline {
    agent any
    environment {
        GITHUB_TOKEN = credentials('jenkins-github') // Updated credential ID
    }
    tools {
        nodejs 'NodeJS' // Ensure Node.js is installed and configured in Jenkins
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/RohitTRathod/DevopsProject.git', 
                    credentialsId: 'jenkins-github' // Updated credential ID
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Deploy') {
            steps {
                sh 'npm start'
            }
        }
    }
    post {
        success {
            slackSend(
                channel: '#devops', 
                color: 'good', 
                message: "Build succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)"
            )
        }
        failure {
            slackSend(
                channel: '#devops', 
                color: 'danger', 
                message: "Build failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)"
            )
        }
    }
}
