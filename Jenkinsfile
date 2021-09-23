pipeline{
    agent any
//    tools {
//      maven 'maven3'
//    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git url: 'https://github.com/bhupendra1988/dockeransiblejenkins'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t bkpandey1388/hariapp:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                 withCredentials([string(credentialsId: 'docker-hub-pass', variable: 'dockerHubPwd')]) {
                    sh "docker login -u bkpandey1388 -p ${dockerHubPwd}"
                }
                
                sh "docker push bkpandey1388/hariapp:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
                sh "su - bpandey"
                sh "cd /var/lib/jenkins/workspace/dockerhub-ansible-jenkins"
                sh "ansible-playbook deploy-docker.yml -i dev.inv"
     //         ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
