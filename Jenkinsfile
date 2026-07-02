pipeline {
<<<<<<< HEAD
    // Top-level agent is any, but we will use Docker agents for specific stages
    agent any

    environment {
        // Docker credentials ID configured in Jenkins
        DOCKER_CREDS = 'dockerhub-creds'
=======
    // Top-level agent is any, but we use Docker agents for specific stages
    agent any

    environment {
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
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
<<<<<<< HEAD
                // Using the Hadolint docker image to scan the Dockerfile
=======
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
                sh 'docker run --rm -i hadolint/hadolint < Dockerfile'
            }
        }

        stage('Build & Test (Docker Agent)') {
<<<<<<< HEAD
            // Run these specific steps inside an isolated NodeJS Docker container
=======
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "Installing dependencies and testing..."
<<<<<<< HEAD
=======
                // Force npm to use a local cache folder to avoid EACCES permission errors
                sh 'npm config set cache $(pwd)/.npm-cache --global'
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
                sh 'npm install'
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
<<<<<<< HEAD
                // Map the Docker socket so Trivy can inspect the locally built image
=======
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
                sh "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity HIGH,CRITICAL ${FULL_IMAGE}"
            }
        }

        stage('Push to Docker Hub') {
            steps {
<<<<<<< HEAD
                // Authenticate and push
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDS, passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER_VAR')]) {
                    sh "echo 'n0H@5hxzxz@987' | docker login -u \$DOCKER_USER_VAR --password-stdin"
                    sh "docker push ${FULL_IMAGE}"
                }
=======
                // Hardcoded authentication
                sh "echo 'n0H@5hxzxz@987' | docker login -u '${DOCKER_USER}' --password-stdin"
                sh "docker push ${FULL_IMAGE}"
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
            }
        }

        stage('Trigger Deployment') {
            steps {
                echo "Triggering downstream deployment pipeline..."
<<<<<<< HEAD
                // Use the Shared Library function just as an example of execution
                DeployToMaster(anyparam: "Triggering deployment for ${env.BRANCH_NAME}")
                
=======
                
                // Shared Library function call
                DeployToMaster(anyparam: "Triggering deployment for ${env.BRANCH_NAME}")
                
>>>>>>> 31593ea (Update Jenkinsfile with complete pipeline code)
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
