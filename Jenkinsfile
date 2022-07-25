pipeline{
    agent any
    environment {
        DOCKER_TAG = getversion()
}
    stages{
        stage('SCM'){
            steps{
                git branch: 'main', credentialsId: 'git-creds', url: 'https://github.com/zessivila/jenkins-kubernetes-example.git'
                
            }
        }
        stage('Build Docker Image'){
            steps{
                sh 'docker build . -t zali45591/nodejsapp:${DOCKER_TAG}'
                
            }
        }
        stage('Push Docker Image'){
            steps{
                withCredentials([string(credentialsId: 'Docker_pwd', variable: 'DokerHubPwd')]) {
                sh 'docker login -u zali45591 -p ${DokerHubPwd}'
                sh 'docker push zali45591/nodejsapp:${DOCKER_TAG}'
                    
                }
            }
        }
        stage('deploy on k8s'){
            steps{
                sshagent(['k8s']) {
                sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml ubuntu@52.90.34.149:/home/ubuntu"
                script{
                   try{
                       sh "ssh ubuntu@52.90.34.149 kubectl apply -f ."
                    }catch(error){
                       sh "ssh ubunntu@52.90.34.149 kubectl create -f ."
                       }
                   } 
                }
            }
        }
    }
} 

def getversion() {
    def tag = sh script:'git rev-parse HEAD' , returnStdout:true
    return tag
}
