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
        DOCKER_CREDENTIALS_ID = credentials('dockerHubCredentials')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RohitTRathod/DevopsProject.git', credentialsId: 'github-credentials'
            }
        }
        stage('Build Docker Image') {
            steps {
                container('docker') {
                    sh 'docker build -t rohittrathod/devops-project:latest .'
                }
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials',
                                                  usernameVariable: 'DOCKER_USERNAME',
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    container('docker') {
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                container('docker') {
                    sh 'docker push rohittrathod/devops-project:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/deployment.yaml'
                }
            }
        }
    }
    post {
        success {
            slackSend(channel: '#internship', color: 'good', message: "Build succeeded: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
        failure {
            slackSend(channel: '#internship', color: 'danger', message: "Build failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}] (<${env.BUILD_URL}|Open>)")
        }
    }
}
