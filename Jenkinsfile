pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "syamks8/myflaskapp"        // Docker Hub image name (without tag)
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"  // Jenkins DockerHub credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/syamaws/my-docker-jenkins-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with BUILD_NUMBER tag
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push()                // push BUILD_NUMBER tag
                        dockerImage.push("latest")        // push latest tag
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Use BUILD_NUMBER to make container name unique
                    def containerName = "my-flask-app-${BUILD_NUMBER}"

                    // Stop and remove existing container with the same name (if any)
                    sh "docker rm -f ${containerName} || true"

                    // Run the new container
                    sh "docker run -d -p 5001:5001 --name ${containerName} ${DOCKER_IMAGE}:latest"

                    // Optional: Print container name for easy reference
                    echo "Container running: ${containerName}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful! Visit http://<jenkins_server_ip>:5001"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
