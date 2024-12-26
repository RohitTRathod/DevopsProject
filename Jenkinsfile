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
        stage('Run Docker Conatainer') {
            steps {
                script {
                    // Run the Docker container
                    sh 'docker pull rohittrathod/devops-project:latest'
                    sh 'docker run -d -p 3000:3000 rohittrathod/devops-project:latest' 
                }
            }
        }
        stage('Slack notification') {
            steps {
                slackSend(channel: '#internship', color: 'good', message: "Successfully Completed ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL})")
            }
        }
    }
    post {
        success {
            // Sending success message to Slack channel
            slackSend(channel: '#internship', color: 'good', message: "Build succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
        failure {
            // Sending failure message to Slack channel
            slackSend(channel: '#internship', color: 'danger', message: "Build failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
    }
}
