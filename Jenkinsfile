pipeline {
    agent any
    environment {
        DOCKER_USERNAME = "vigneshrepo23"
        APP_NAME = "python_image"
        IMAGE_NAME = "${DOCKER_USERNAME}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_CRED = "dockertoken"
    }
    stages {
        stage ('cleanup ws') {
            steps {
                cleanWs()
            }
        }
        stage ('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vigneshrepo23/py-gitops-ci.git'
            }
        }
        stage ('build image') {
            steps {
                script {
                   myImage = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('push image to hub') {
            steps {
                script {
                    docker.withRegistry('', 'dockertoken') {
                        myImage.push("${BUILD_NUMBER}")
                        myImage.push("latest")
                    }
                }
            }
        }
        stage ('rmi image') {
            steps {
                script {
                    sh 'docker rmi ${IMAGE_NAME}:${BUILD_NUMBER}'
                    sh 'docker rmi ${IMAGE_NAME}:latest'
                }
            }
        }
        stage ("update k8s deploy manifest") {
            steps {
                script {
                    sh """
                        cat deployment.yml
                        sed -i 's/${IMAGE_NAME}.*/${IMAGE_NAME}:${BUILD_NUMBER}/g' deployment.yml
                        cat deployment.yml
                    """
                }
            }
        }
    }
}