pipeline {
    // Top-level agent is any, but we use Docker agents for specific stages
    agent any

    environment {
        DOCKER_USER = 'kaji777'
        
        // Define distinct image names based on the branch
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG = 'v1.0'
        FULL_IMAGE = "${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Lint Dockerfile (Hadolint)') {
            steps {
                echo "Running Hadolint..."
                // Using the Hadolint docker image to scan the Dockerfile
                sh 'docker run --rm -i hadolint/hadolint < Dockerfile'
            }
        }

        stage('Build & Test (Docker Agent)') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "Installing dependencies and testing..."
                // Pass the cache location directly to the command, skipping system config files
                sh 'npm install --cache $(pwd)/.npm-cache'
                sh 'npm test --passWithNoTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Image: ${FULL_IMAGE}"
                sh "docker build -t ${FULL_IMAGE} ."
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                echo "Scanning image for vulnerabilities..."
                // Map the Docker socket so Trivy can inspect the locally built image
                sh "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity HIGH,CRITICAL ${FULL_IMAGE}"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                // Hardcoded authentication
                sh "echo 'n0H@5hxzxz@987' | docker login -u '${DOCKER_USER}' --password-stdin"
                sh "docker push ${FULL_IMAGE}"
            }
        }

        stage('Trigger Deployment') {
            steps {
                echo "Triggering downstream deployment pipeline..."
                
                // Shared Library function call
                DeployToMaster(anyparam: "Triggering deployment for ${env.BRANCH_NAME}")
                
                // Conditionally trigger the right downstream pipeline
                script {
                    if (env.BRANCH_NAME == 'main') {
                        build job: 'Deploy_to_main', wait: false
                    } else if (env.BRANCH_NAME == 'dev') {
                        build job: 'Deploy_to_dev', wait: false
                    }
                }
            }
        }
    }
    
    post {
        always {
            // Clean up local images to save space on the Linux VM
            sh "docker rmi -f ${FULL_IMAGE} || true"
            sh "docker logout"
        }
    }
}
