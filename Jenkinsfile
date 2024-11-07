pipeline {
    agent any
        
            environment {
                IMAGE_NAME = 'natinael/hello-world'
                DOCKER_TAG = "${BUILD_NUMBER}"
                SERVICE_NAME = "hello-world"
               } 
    stages {
        
        stage('fetch github code') {
            steps {
                git branch: 'main', url: 'https://github.com/NatinaelSeifu/hello-world.git'
        
              }
            }
            
        stage('Build Docker Image') {
            steps {
                // Build the Docker image from the Dockerfile in the project root
                script {
                    sh "docker build -t ${IMAGE_NAME}:${DOCKER_TAG} ."
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    // Access the credentials stored in DOCKER_REGISTRY_CREDENTIALS
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_REGISTRY_CREDENTIALS', 
                                                      usernameVariable: 'DOCKER_USERNAME', 
                                                      passwordVariable: 'DOCKER_PASSWORD')]) {
                        // Perform Docker login using shell command
                        sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin https://index.docker.io/v1/"
                        
                        // Push the Docker image
                        sh "docker push ${IMAGE_NAME}:${DOCKER_TAG}"
                        
                    }
                }
            }
        }


        
        stage('Check Docker Service Update') {
            steps {
                script {
                    
                    sh "docker service update --force --image ${IMAGE_NAME}:${env.BUILD_NUMBER} ${SERVICE_NAME}"
                    
                    def updateStatus = sh(script: "docker service inspect ${SERVICE_NAME} --format '{{.UpdateStatus.Message}}'", returnStdout: true).trim()
                    
                    if (updateStatus != "update completed") {
                        echo "Update status is failed. Rolling back the service."
                        
                        sh "docker service update --rollback ${serviceName}"
                    } else {
                        echo "Update completed successfully."
                    }
                }
            }
        }

        
    }

}

    