pipeline {
    agent {
        docker {
            image 'node:18'  // The agent runs in a Node.js Docker container to support Next.js
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // Bind Docker socket to use Docker inside Jenkins
        }
    }
    environment {
        DOCKER_IMAGE = "natinael/hello-world"  // Replace with your Docker registry and image name
        DOCKER_REGISTRY_CREDENTIALS = 'dockerhub'  // Jenkins ID for Docker Hub credentials
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/NatinaelSeifu/hello-world'  // Check out your repository
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_REGISTRY_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push()
                       // docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push('latest')  // Optional: Push as latest tag
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['server']) {  // Jenkins ID for SSH credentials to the deployment server
                    sh '''
                    ssh -o StrictHostKeyChecking=no azureuser@127.179.10.10 "docker service update --force --image ${DOCKER_IMAGE}:${env.BUILD_NUMBER} hello-world"
                    '''
                }
            }
        }
    }
    post {
        always {
            cleanWs()  // Clean workspace after each build
        }
    }
}
