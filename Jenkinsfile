// authored by : Nimit
pipeline {
    agent any
    environment {
        BUILD_TIMESTAMP = "${new java.text.SimpleDateFormat('yyyyMMddHHmm').format(new Date())}"
        DOCKERHUB_PASS = credentials('docker-cred') 
    }
    stages {
        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKERHUB_PASS', usernameVariable: 'DOCKERHUB_USER')]) {
                        sh '''#!/bin/bash
                            echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        '''
                    }
                }
            }
        }
        stage("Checkout") {
            steps {
                checkout scm
                sh 'ls -al'
                sh 'pwd'
                sh("docker build --tag njogani/swe645_hw2:${BUILD_TIMESTAMP} .")
                sh("echo ${BUILD_TIMESTAMP}")
            }   
        }
        stage("Push docker image") {
            steps {
                sh("docker push njogani/swe645_hw2:${BUILD_TIMESTAMP}")
            }
        }
        stage("Deploy docker image to rancher cluster with 3 pod using nodeport") {
            steps {
                sh("sudo kubectl set image deployment/survey container-0=njogani/swe645_hw2:${BUILD_TIMESTAMP} -n hw2")
            }
        }
    }
}
