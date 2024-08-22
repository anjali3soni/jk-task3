pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "anjalisoni12/react-app-image:${BUILD_NUMBER}"
        SLACK_USER_ID = 'U07CB5WDYVB' 
        SLACK_CREDENTIALS_ID = 'slack-bot'
    }

    stages {
        stage('Download Code from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/anjali3soni/jk-task3.git'
            }
        }

        stage('Docker Image Build') {
            steps {
                sh """
                   docker build -t $DOCKER_IMAGE -f /var/lib/jenkins/workspace/jk-t3/web3/Dockerfile /var/lib/jenkins/workspace/jk-t3/
                """
            }
        }

        stage('DockerHub Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                           docker login -u $DOCKER_USER -p $DOCKER_PASS
                           docker push $DOCKER_IMAGE
                        """
                    }
                }
            }
        }

        stage('Docker Compose Update') {
            steps {
                sh """
                   cd /var/lib/jenkins/workspace/jk-t3/web3
                   sed -i 's|image: .*|image: ${DOCKER_IMAGE}|' docker-compose.yml
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                   docker-compose -f /var/lib/jenkins/workspace/jk-t3/web3/docker-compose.yml up -d
                """
            }
        }
    }
    post {
        failure {
            // Send Slack notification on failure
            slackSend (
                channel: "${SLACK_USER_ID}",  // Send notification to the Slack user ID
                color: 'danger',
                message: """
                    *Deployment Pipeline Failed!*
                    
                    *Job:* ${env.JOB_NAME}
                    *Build Number:* ${env.BUILD_NUMBER}
                    *Build URL:* ${env.BUILD_URL}
                    *Failure Reason:* ${currentBuild.description}
                """,
                tokenCredentialId: "${SLACK_CREDENTIALS_ID}"
            )
        }

        success {
            // Send Slack notification on success
            slackSend (
                channel: "${SLACK_USER_ID}",  // Send notification to the Slack user ID
                color: 'good',
                message: """
                    *Deployment Pipeline Succeeded!*
                    
                    *Job:* ${env.JOB_NAME}
                    *Build Number:* ${env.BUILD_NUMBER}
                    *Build URL:* ${env.BUILD_URL}
                """,
                tokenCredentialId: "${SLACK_CREDENTIALS_ID}"
            )
        }
    }
}
