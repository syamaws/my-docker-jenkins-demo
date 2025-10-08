
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your_dockerhub_username/my-docker-demo"  // Change this
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"  // ID from Jenkins Credentials
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/yourusername/my-docker-jenkins-demo.git'  // or your repo
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push()
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Stop and remove existing container (if any)
                    sh "docker rm -f my-docker-demo || true"

                    // Run the new image
                    sh "docker run -d -p 5000:5000 --name my-docker-demo ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful! Visit http://<jenkins_server_ip>:5000"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
