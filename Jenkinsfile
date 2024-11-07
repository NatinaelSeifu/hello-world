pipeline {
    
    environment {
        APP_REPOSITORY_URL = 'https://github.com/NatinaelSeifu/hello-world'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    agent any
        
            
        stages {
            
            stage('Checkout application repo') {
                steps {
                    checkout([$class: 'GitSCM', 
                      branches: [[name: 'staging']], 
                      userRemoteConfigs: [[
                        url: "${APP_REPOSITORY_URL}",
                        credentialsId: 'dockerhub'
                      ]]
                    ])
                }
            stage('Build Docker Image') {
                steps {
                    // Build the Docker image from the Dockerfile in the project root
                    script {
                        sh "docker build -t frnt-web-2:latest ."
                    }
                }
            }
            
            stage('Tag the image') {
                steps {
                    
                    script {
                        sh "docker tag frnt-web-2:latest 480053995985.dkr.ecr.us-east-2.amazonaws.com/frnt-web-2:latest"
                    }
                }
            }
            
            stage('Login to ECR registry') {
                steps {
                    
                    script {
                        sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 480053995985.dkr.ecr.us-east-2.amazonaws.com"
                    }
                }
            }
            
            stage('Push Image to ECR') {
                steps {
                    
                    script {
                        sh "docker push "
                    }
                }
            }
            
            stage('Deploy to ECS Fargate') {
                steps {
                    
                    script {
                        sh "aws ecs update-service --cluster phase-2-project --service frnt-web-2 --force-new-deployment"
                    }
                }
            }
            post {
                success {
                    echo 'Pipeline succeeded! Your ECS Fargate is built and pushed.'
                }
                failure {
                    echo 'Pipeline failed! Please check the logs for more information.'
                }
            }
        }
    }



    