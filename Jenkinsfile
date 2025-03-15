pipeline {
    agent any

    environment {
        // Define environment variables for reuse
        DOCKER_IMAGE_NAME = "docker-reactjs"
        CONTAINER_NAME = "react-container"
        SEMGREP_API_KEY = credentials('semgrep-api-key')
    }

    stage('Semgrep SAST Scan') {
            steps {
                script {
                    sh """
                        echo "Running Semgrep SAST scan..."
                        docker run \\
                            -e SEMGREP_APP_TOKEN=${SEMGREP_API_KEY} \\
                            --rm \\
                            -v "${PWD}:/src" \\
                            semgrep/semgrep \\
                            semgrep ci
                    """
                }
            }
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
                        trivy image --exit-code 1 --severity HIGH,CRITICAL ${DOCKER_IMAGE_NAME}:latest
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