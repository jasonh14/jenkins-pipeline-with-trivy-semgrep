pipeline {
    agent any

    environment {
        // Define environment variables for reuse
        DOCKER_IMAGE_NAME = "docker-reactjs"
        CONTAINER_NAME = "react-container"
        TRIVY_VERSION = "latest" // Specify the Trivy version to use
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

        stage('Scan Docker Image') {
            steps {
                script {
                    // Scan the Docker image for vulnerabilities 
                    // Add --exit-code 1 if you want to exit if severity HIGH,CRITICAL is found
                    sh """
                        echo "Scanning Docker image: ${DOCKER_IMAGE_NAME}:latest"
                        trivy image --severity HIGH,CRITICAL ${DOCKER_IMAGE_NAME}:latest || true
                    """
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