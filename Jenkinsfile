pipeline {
    agent any
    environment {
        DOCKER_TAG = getDockerTag()
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sshagent(['s01-devs']) {
                sh "docker build . -t smranjan/gjdk_01:${DOCKER_TAG}"
                }
            }
        }
        stage('DockerHub Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-smranjan'), variable: 'dockerHubsmranjanPwd'])) {
                sh "docker login -u smranjan -p ${dockerHubsmranjanPwd}"
                sh "docker push smranjan/gjdk_01:${DOCKER_TAG}"
                } 
            }
        }
        stage('Deploy to k8s') {
            steps {
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['s01-devs']) {
		    sh "ssh devs@10.10.1.10 mkdir /home/devs/k8s"
                    sh "scp -o StrictHostKeyChecking=no pods_services.yaml devs@10.10.1.10:/home/devs/k8s/"
                }
            }
        }
    }
}

def getDockerTag() {
    //git url: 'https://github.com/smranjan/git-jenkins-docker.git', branch: 'master'
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
