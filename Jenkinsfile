pipeline{
    agent any
     environment {
        DOCKER_IMAGE = "anjalisoni12/react-app-image:${BUILD_NUMBER}"
       
    }
    stages{
        stage('download code from github'){
            steps{
                git branch: 'react-app', url: 'https://github.com/anjali3soni/jk-task3.git'
            }
        }
         stage('docker image build'){
            steps{
               sh 'docker build -t ${DOCKER_IMAGE}  react-deploy/'
            }
        }
         stage('dockerhub login'){
            steps{
                script{
               docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') 
               {
                sh 'docker push ${DOCKER_IMAGE}'
               }
            }}
        }
         
        stage('docker compose update'){
            steps{
               sh "sed -i 's|image: .*|image: ${DOCKER_IMAGE}|' react-deploy/docker-compose.yml"
            }
        }
         stage('deploy'){
            steps{
               sh  '''docker-compose down
                    docker-compose up -d'''
            }
        }
    }
}