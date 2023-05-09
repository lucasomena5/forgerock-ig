pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
        repoName = "devforge1"
    }
    
    stages {
        stage('Authenticate with Docker Hub') {
		    steps {
                script {
                    sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Checking pre-requisites...\""""
			     
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''docker login -u $USERNAME -p $PASSWORD'''
                    }
                }
		    }
		}
        
        stage('Build Docker Image') {
            steps {
                script {

                    def applicationRepo = "${env.WORKSPACE}/identity-gateway/application"
                    def osRepo = "${env.WORKSPACE}/identity-gateway/os"

                    sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build docker image...\""""
                
                    sh "ls -lah ${env.WORKSPACE}"
                    sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building application base image...\""""
                    sh "cd ${applicationRepo}"

                    sh "docker build . -t ig:v${BUILD_NUMBER}"
                    sh "docker tag ig:v${BUILD_NUMBER} ${repoName}/ig:v${BUILD_NUMBER}"
                    sh "docker ps -a"
                    //sh "docker push devforge1/ig:v${BUILD_NUMBER}"
                }
            }
        }
    }
}
