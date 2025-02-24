pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
        stage('Install Dependencies') {
            steps {
                // Change to the directory containing package.json if needed
                dir('app') {  // Adjust 'app' to your actual directory
                    bat 'npm install'  // Use 'sh' instead of 'bat' for Linux agents
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', 
                                                  usernameVariable: 'DOCKER_USERNAME', 
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        // Log in to Docker Hub, build and push the Docker image
                        bat "docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%"
                        bat 'docker build -t rohittrathod/ibm-project .'
                        bat 'docker tag rohittrathod/ibm-project rohittrathod/ibm-project:latest'
                        bat 'docker push rohittrathod/ibm-project:latest'
                    }
                }
            }
        }
        stages {
        stage('Deploy to Minikube') {
            steps {
                withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG')]) {
                    script {
                        // Apply the Kubernetes deployment and service YAML files
                        sh 'minikube service ibmapp-service' 
                      
                    }
                }
            }
        }
    }
        stage('Slack Notification') {
            steps {
                script {
                    // Send a Slack notification
                    slackSend(channel: '#internship', color: 'good', message: "Successfully Completed ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL})")
                }
            }
        }
    }
    post {
        success {
            // Sending success message to Slack channel
            script {
                slackSend(channel: '#internship', color: 'good', message: "Build and deployment succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
            }
        }
        failure {
            // Sending failure message to Slack channel
            script {
                slackSend(channel: '#internship', color: 'danger', message: "Build and deployment failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
            }
        }
    }
}
