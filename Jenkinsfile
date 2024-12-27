pipeline {
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: docker
                image: docker:latest
                command:
                - cat
                tty: true
              - name: kubectl
                image: bitnami/kubectl:latest
                command:
                - cat
                tty: true
            '''
        }
    }
    environment {
        GITHUB_TOKEN = credentials('jenkins-github') // Ensure this credential ID is correct
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
         stage('Run Docker Container') {
            steps {
                // Run the Docker container
                bat 'docker run -d -p 8084:8080 rohittrathod/devops-project'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                container('kubectl') {
                    // Load the Docker image into Minikube
                    sh 'minikube image load yourapp:latest'
                    
                    // Update the Kubernetes deployment to use the locally built image
                    sh '''
                    kubectl set image deployment/yourapp-deployment yourapp=yourapp:latest
                    kubectl rollout status deployment/yourapp-deployment
                    '''
                }
            }
        }
    }
    post {
        success {
            // Sending success message to Slack channel
            slackSend(channel: '#internship', color: 'good', message: "Build and deployment succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
        failure {
            // Sending failure message to Slack channel
            slackSend(channel: '#internship', color: 'danger', message: "Build and deployment failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
    }
}
