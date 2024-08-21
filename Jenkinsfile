pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "anjalisoni12/react-app-image:${BUILD_NUMBER}"
    }
    stages {
        stage('Download Code from GitHub') {
            steps {
                // Clone the GitHub repository
                git branch: 'main', url: 'https://github.com/anjali3soni/jk-task3.git'
            }
        }
        stage('Verify Build Context') {
            steps {
                // List files to verify that the context is correct and package.json is present
                sh 'ls -R'
            }
        }
        stage('Docker Image Build') {
            steps {
                // Build the Docker image
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }
        stage('Docker Hub Login') {
            steps {
                script {
                    // Log in to Docker Hub
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        sh 'docker push ${DOCKER_IMAGE}'
                    }
                }
            }
        }
        stage('Docker Compose Update') {
            steps {
                // Update the docker-compose.yml file with the new image tag
                sh "sed -i 's|image: .*|image: ${DOCKER_IMAGE}|' docker-compose.yml"
            }
        }
        stage('Deploy') {
            steps {
                // Bring down and then bring up the Docker containers using docker-compose
                sh '''
                    docker-compose down
                    docker-compose up -d
                '''
            }
        }
    }
}
