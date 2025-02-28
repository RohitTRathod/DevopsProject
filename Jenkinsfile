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
                dir('app') {  // Change to the directory containing package.json
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
                        
                        bat 'docker tag rohittrathod/pretest rohittrathod/ibm-project:latest'
                        bat 'docker push rohittrathod/ibm-project:latest'
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG')]) {
                    script {
                        // Apply the Kubernetes deployment and service YAML files
                        bat 'kubectl apply -f kubernetes/deployment.yaml --validate=false'  // Adjust the path as necessary
                        bat 'kubectl apply -f kubernetes/service.yaml --validate=false'     // Adjust the path as necessary
                    }
                }
            }
        }

        stage('Slack Notification') { 
            steps { 
                script { 
                    slackSend(channel: '#internship', color: 'good', message: """Successfully Completed 
${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)""") 
                } 
            } 
        } 
    } 

    post { 
        success { 
            script { 
                slackSend(channel: '#internship', color: 'good', message: """Build and deployment succeeded: 
${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)""") 
            } 
        } 
        failure { 
            script { 
                slackSend(channel: '#internship', color: 'danger', message: """Build and deployment failed: 
${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)""") 
            } 
        } 
    } 
}