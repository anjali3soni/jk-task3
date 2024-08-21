pipeline{
    agent any
     environment {
        DOCKER_IMAGE = "anjalisoni12/react-app-image:${BUILD_NUMBER}"
       
    }
    stages{
        stage('download code from github'){
            steps{
                git branch: 'main', url: 'https://github.com/anjali3soni/jk-task3.git'
            }
        }
         stage('docker image build'){
            steps{
               sh 'docker build -t ${DOCKER_IMAGE} .'
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
               sh "sed -i 's|image: .*|image: ${DOCKER_IMAGE}|' /var/lib/jenkins/jk-t3/web3/docker-compose.yml"
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