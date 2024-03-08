pipeline {
    agent any
    environment {
        DOCKER_USERNAME = "vigneshrepo23"
        APP_NAME = "python_image"
        IMAGE_NAME = "${DOCKER_USERNAME}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_CRED = "dockertoken"
        GITURL = "https://github.com/vigneshrepo23/py-gitops-ci.git"
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
            steps {// sed command - stream editor - find & replace
                script { // s - string/replace image with any build number with current build number/g global
                    sh """
                        cat deployment.yml
                        sed -i 's/${APP_NAME}.*/${APP_NAME}:${BUILD_NUMBER}/g' deployment.yml
                        cat deployment.yml
                    """
                }
            }
        }
        stage ('update version in github') {
            steps { // withcrendentials snippet - git username & password
                    script {
                        """
                        git config --global user.name "vignesh"
                        git config --global user.mail "vignesh@gmail.com"
                        git add deployment.yml
                        git commit -m 'update deployment.yml with current build number'
                        """
                         withCredentials([gitUsernamePassword(credentialsId: 'gitcred', gitToolName: 'Default')]) {
                         git push ${GITURL} HEAD:main
                        } 
                    }
             }
        }
    }
}
