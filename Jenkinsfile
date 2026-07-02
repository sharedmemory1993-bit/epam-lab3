pipeline {
    agent any

    // Use the NodeJS plugin configured in Jenkins (assuming it's named 'node')
    tools {
        nodejs 'node'
    }

    environment {
        // Conditional logic: If branch is main, port is 3000. Otherwise (dev), port is 3001.
        HOST_PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "my-app-${env.BRANCH_NAME}"
        CONTAINER_NAME = "app-container-${env.BRANCH_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Multibranch automatically checks out the branch, 
                // but explicitly defining scm ensures we have the code.
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo "Building environment for branch: ${env.BRANCH_NAME}"
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                echo "Running automated tests..."
                // Replace with actual test command if different
                sh 'npm test --passWithNoTests' 
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${IMAGE_NAME}"
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Deploying to port ${HOST_PORT}"
                
                // Stop and remove the old container if it exists to avoid port conflicts
                sh "docker rm -f ${CONTAINER_NAME} || true"
                
                // Run the new container, mapping the dynamic HOST_PORT to the internal container port (e.g., 3000)
                sh "docker run -d -p ${HOST_PORT}:3000 --name ${CONTAINER_NAME} ${IMAGE_NAME}"
            }
        }
    }
}
