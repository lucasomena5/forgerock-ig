properties(
	[
		buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')), 
        disableConcurrentBuilds(),
		// parameters(
		// 	[
		// 		choice(
		// 			choices: 
		// 			[''], 
		// 			description: 'Select the version', 
		// 			name: 'VERSION'
		// 		)
		// 	]
		// )
	]
)

pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
        gitHubCredential = credentials('jenkins_prudential_key')
        repoName = "devforge1"
        registryCredential = 'docker-hub-credentials'
    }
    
    stages {

        stage('Initialization') {
            steps {
                script {
				
					echo "Clean Workspace ..."
					ws(WORKSPACE) {
						cleanWs()
					}

					String buildID = "#${BUILD_NUMBER} - product".toString()

					currentBuild.displayName = buildID
					
                }
            }
        }

        stage('Authenticate with Docker Hub') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            
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

                    def igApplicationRepo = "${env.WORKSPACE}/identity-gateway/ig-application"
                    def baseImageRepo = "${env.WORKSPACE}/identity-gateway/ig-baseimage"
                    sh "cat ${baseImageRepo}/Dockerfile"
                    
                    dir("${baseImageRepo}"){

                        sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building IG base docker image...\""""
                        def dockerImage = docker.build("${repoName}/forgerock-temurin:11", ".")
                        def baseImageName = dockerImage.id

                        sh "echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` IG Base Image ID: ${baseImageName}\""""

                        docker.withRegistry('', "${registryCredential}") {
                            dockerImage.push()
                        }
                        
                        sh "docker images"
                    }

                    dir("${igApplicationRepo}"){

                        sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build IG docker image...\""""
                        sh """sed -i 's/__BASEIMAGE_NAME__/${baseImageName}/g' ${igApplicationRepo}/Dockerfile"""
                        
                        def dockerImage = docker.build("${repoName}/ig:v${BUILD_NUMBER}", ".")
                        def igImageName = dockerImage.id
                        sh "echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` IG Application Image ID: ${igImageName}\""""

                        docker.withRegistry('', "${registryCredential}") {
                            dockerImage.push()
                        }
                        
                        sh "docker images"
                    }

                    
                }
            }
        }

        // stage('Remove Unused docker image') {
        //   steps{
        //     sh "docker rmi ${repoName}/ig:v${BUILD_NUMBER}"
        //   }
        // }
    }
}
