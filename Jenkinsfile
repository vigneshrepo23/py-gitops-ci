pipeline {
    agent any
    environment {
        DOCKER_USERNAME = "vigneshrepo23"
        APP_NAME = "python_image"
        IMAGE_NAME = "${DOCKER_USERNAME}" + "${APP_NAME}"
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
                    my_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('push image to hub') {
            steps {
                script {
                    docker.withRegistry('','dockertoken') {
                        my_image.push{"$BUILD_NUMBER"}
                        my_image.push{"latest"}

                    }
                }
            }
        }
    }
}