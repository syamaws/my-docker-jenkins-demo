pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "syamks8/myflaskapp:1.0"              // Docker Hub image name
        DOCKER_CREDENTIALS_ID = "dockerhub-creds"     // Jenkins DockerHub credentials ID
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
