pipeline {
    agent any

    environment {
        // Define environment variables for reuse
        DOCKER_IMAGE_NAME = "docker-reactjs"
        CONTAINER_NAME = "react-container"
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the Git repository
                git 'https://github.com/jasonh14/jenkins-test.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    docker.build("${DOCKER_IMAGE_NAME}:latest", ".")
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop and remove any existing container with the same name
                    sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                    """

                    // Run the new container
                    docker.run(
                        image: "${DOCKER_IMAGE_NAME}:latest",
                        args: "-d -p 80:80 --name ${CONTAINER_NAME}"
                    )
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}