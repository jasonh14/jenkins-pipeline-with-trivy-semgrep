pipeline {
    agent any

    environment {
        // Define environment variables for reuse
        DOCKER_IMAGE_NAME = "docker-reactjs"
        CONTAINER_NAME = "react-container"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using a shell command
                    sh '''
                        docker build -t ${DOCKER_IMAGE_NAME}:latest .
                    '''
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

                    // Run the new container in detached mode (-d)
                    sh """
                        docker run -d -p 80:80 --name ${CONTAINER_NAME} ${DOCKER_IMAGE_NAME}:latest
                    """
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