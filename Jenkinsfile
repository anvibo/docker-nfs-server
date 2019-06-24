pipeline {
   environment {
    registry = "anvibo/nfs-server"
    registryCredential = 'dockerhub'
    dockerImage = ''
    tag = ''
  }

  agent {
    kubernetes {
      label 'jenkins-docker'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:1.11
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
    }
  }
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/anvibo/docker-nfs-server.git'
      }
    }
    stage('Build image' ) {
      steps{
        container('docker') {
                withDockerRegistry(registry: [credentialsId: 'dockerhub']) {
                  script {
                    dockerImage = docker.build(registry, "-f Dockerfile .")
                  }
                }
            }
      }
    }
    stage('Push development image') {
      steps{
        container('docker') {
                withDockerRegistry(registry: [credentialsId: 'dockerhub']) {
                  script {
                    dockerImage.push("dev")
                    sh "docker rmi $registry:dev"
                  }
                }
            }
      }
    }
    stage('Push release image') {
      when { tag "v*" }
      steps{
        container('docker') {
                withDockerRegistry(registry: [credentialsId: 'dockerhub']) {
                  script {
                    dockerImage.push()
                    dockerImage.push("$TAG_NAME")
                    sh "docker rmi $registry:$TAG_NAME"
                    sh "docker rmi $registry"
                  }
                }
            }
      }
    }

  }
}