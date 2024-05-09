def dockerImage

pipeline {
    agent any

    environment {
        PATH = "$PATH:/usr/local/bin"
    }
    
    tools {
        nodejs "node"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH_NAME}", credentialsId: 'jenkinsId', url: 'https://github.com/HaykHSS/cicd-pipeline.git'
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
        
        stage('Build Docker Image') {
            steps {
                sh 'docker stop $(docker ps -q) || true'
                sh 'docker rm $(docker ps -aq) || true'
                
                script {
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
