pipeline {
    agent any
    environment {
        DOCKER_TAG = getDockerTag()
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build . -t mylabfs/gjd_pl_03:${DOCKER_TAG}"
            }
        }
        // stage('DockerHub Push') {
        //     steps {
        //         withCredentials([string(credentialsId: 'docker-hub-mylabfs'), variable: 'dockerHubMylabfsPwd'])) {
        //         sh "docker login -u mylabfs -p ${dockerHubMylabfsPwd}"
        //         sh "docker push mylabfs/myapp:${DOCKER_TAG}"
        //         } 
        //     }
        // }
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
