pipeline {
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: docker
                image: docker:latest  // Docker image for building
                command:
                - cat
                tty: true
              - name: kubectl
                image: bitnami/kubectl:latest  // kubectl image for Kubernetes commands
                command:
                - cat
                tty: true
            '''
        }
    }
    environment {
        GITHUB_TOKEN = credentials('jenkins-github')  // Ensure this credential ID is correct
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    // Build the Docker image
                    sh 'docker build -t rohittrathod/devops-project:latest .'  // Replace with your Docker image name
                }
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', 
                                                  usernameVariable: 'DOCKER_USERNAME', 
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    container('docker') {
                        // Log in to Docker Hub
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    // Push the Docker image to Docker Hub
                    sh 'docker push rohittrathod/devops-project:latest'  // Replace with your Docker image name
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                container('kubectl') {
                    // Deploy to Kubernetes
                    sh 'kubectl apply -f kubernetes/deployment.yaml'  // Ensure this path is correct
                }
            }
        }
        stage('Slack Notification') {
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