pipeline {
    agent any
    
    tools {
        // Define NodeJS tool installation
        nodejs "node"
    }
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from Git repository
                git branch: "${BRANCH_NAME}", credentialsId: 'jenkinsId', url: 'https://github.com/HaykHSS/cicd-pipeline.git'
            }
        }
        
        stage('Build') {
            steps {
                // Build NodeJS application
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                // Test NodeJS application
                sh 'npm test'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                // Stop and remove previously running containers
                sh 'docker stop $(docker ps -q) || true'
                sh 'docker rm $(docker ps -aq) || true'
                
                // Build Docker images based on the branch
                script {
                    def dockerImage
                    if (env.BRANCH_NAME == 'main') {
                        dockerImage = 'nodemain:v1.0'
                    } else if (env.BRANCH_NAME == 'dev') {
                        dockerImage = 'nodedev:v1.0'
                    }
                    sh "docker build -t ${dockerImage} ."
                }
            }
        }
        
        stage('Deploy') {
            steps {
                // Run application in Docker container based on the branch
                script {
                    def exposePort
                    if (env.BRANCH_NAME == 'main') {
                        exposePort = 3000
                    } else if (env.BRANCH_NAME == 'dev') {
                        exposePort = 3001
                    }
                    sh "docker run -d --expose ${exposePort} -p ${exposePort}:3000 ${dockerImage}"
                }
            }
        }
    }
}
