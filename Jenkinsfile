pipeline {
    agent any
    environment {
        GITHUB_TOKEN = credentials('jenkins-github')
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
        stage('Build') {
            steps {
                // Simulating a build process using echo (since npm is not used)
                echo 'Simulating npm install (dependencies are not installed)'
            }
        }
        stage('Test') {
            steps {
                // Simulating test process using echo
                echo 'Simulating npm test (no tests are run)'
            }
        }
        stage('Deploy') {
            steps {
                // Simulating deployment using echo
                echo 'Simulating npm start (no actual deployment)'
            }
        }
        stage('Slack notification'){
            steps{
                slackSend channel: '#internship', color: 'good' ,message: "Successfully Completed ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}"

            }
        }
    }
    
}
