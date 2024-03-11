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
        stage ('trigger cd') {
            steps {
                script {

                   sh "curl -v -k --user admin:1154e205233afdc1b813ae4300547c0323 -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://44.206.240.216:8080/job/test-cd/buildWithParameters?token=trigger-remote'" 
               //  sh "curl -v -k --user vicky:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-3-95-185-75.compute-1.amazonaws.com:8080/job/register-cd/buildWithParameters?token=gitops-token'"

                }
            }
        }

           // CD PART SCRIPT USING SAME GIT REPO

        // stage ("update k8s deploy manifest") {
        //     steps {// sed command - stream editor - find & replace
        //         script { // s - string/replace image with any build number with current build number/g global
        //             sh """
        //                 cat deployment.yml
        //                 sed -i 's/${APP_NAME}.*/${APP_NAME}:${BUILD_NUMBER}/g' deployment.yml
        //                 cat deployment.yml
        //             """
        //         }
        //     }
        // }
        // stage ('update version in github') {
        //     steps { // withcrendentials snippet - git username & password
        //         script {
        //             sh """
        //             git config --global user.name "vignesh"
        //             git config --global user.mail "vignesh@gmail.com"
        //             git add deployment.yml
        //             git commit -m 'update deployment.yml with current build number'
        //             """
        //             withCredentials([gitUsernamePassword(credentialsId: 'gitcred', gitToolName: 'Default')]) {

        //             sh "git push ${GITURL} main"
        //             } 
        //         }
        //     }
        // }
    }
}
