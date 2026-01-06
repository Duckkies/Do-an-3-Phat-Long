pipeline {
    agent any

    environment {
        // --- CONFIGURATION VARIABLES (PLEASE UPDATE YOUR INFO) ---
        
        // 1. Your Docker Hub Username (Not Student ID, unless you registered with it)
        DOCKERHUB_USERNAME = 'zanokuro' 
        
        // 2. Name of the Docker Image
        IMAGE_NAME = 'my-web-app-23127411'
        
        // 3. Credentials ID stored in Jenkins (Keep this as 'dockerhub-id')
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-id'
        
        // 4. Container Name
        CONTAINER_NAME = 'web-server-23127411'
        
        // 5. Port on the Host machine (VM) mapped to the container
        HOST_PORT = '8090'
    }

    stages {
        // Stage 1: Get code from Github
        stage('1. Checkout Code') {
            steps {
                checkout scm
                echo 'Code successfully checked out from Github!'
            }
        }

        // Stage 2: Build and Push Image to Docker Hub
        stage('2. Build & Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub using stored credentials
                    docker.withRegistry('', DOCKERHUB_CREDENTIALS_ID) {
                        
                        echo 'Building Docker Image...'
                        // Command: docker build -t user/image-name:version .
                        def customImage = docker.build("${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${BUILD_NUMBER}")
                        
                        echo 'Pushing Image to Docker Hub...'
                        // Push image with the build number tag (e.g., v1, v2)
                        customImage.push()
                        // Push the 'latest' tag
                        customImage.push('latest')
                    }
                }
            }
        }

        // Stage 3: Deploy Container
        stage('3. Deploy to Container') {
            steps {
                sh '''
                echo "Deploying the latest version..."
                
                # Stop the running container if it exists (to avoid name conflict)
                docker stop ${CONTAINER_NAME} || true
                
                # Remove the stopped container
                docker rm ${CONTAINER_NAME} || true
                
                # Run the new container
                # -d: detach mode (run in background)
                # -p: map host port (8090) to container port (80)
                docker run -d -p ${HOST_PORT}:80 --name ${CONTAINER_NAME} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest
                '''
            }
        }
    }
    
    post {
        success {
            echo 'SUCCESS: Deployment completed successfully.'
            echo 'You can access the website at: http://<VM_IP_ADDRESS>:8090'
        }
        failure {
            echo 'FAILURE: Something went wrong. Please check the Console Output for details.'
        }
    }
}