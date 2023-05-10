properties(
	[
		buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')), 
        disableConcurrentBuilds()
	]
)

pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
        gitHubCredential = credentials('jenkins_prudential_key')
        repoName = "devforge1"
        registryCredential = 'docker-hub-credentials'
        baseImageRepo = "${env.WORKSPACE}/identity-gateway/ig-baseimage"
        igApplicationRepo = "${env.WORKSPACE}/identity-gateway/ig-application"
        baseImageName = "${repoName}/forgerock-temurin:11"
    }
    
    stages {

        stage('Initialization') {
            steps {
                script {
				
					echo """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Clean Workspace ...\""""
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
        
        stage('Build Base Image') {
            steps {
                script {
                    try {

                        sh "cat ${baseImageRepo}/Dockerfile"

                        dir("${baseImageRepo}"){

                            sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building IG base docker image...\""""

                            def dockerBaseImage = docker.build("${baseImageName}", ".")

                            docker.withRegistry('', "${registryCredential}") {
                                dockerBaseImage.push()
                            }

                            sh "docker images"
                        }

                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                    
                }
            }
        }

        stage('Build IG Image') {
            steps {
                script {
                    try {
                        dir("${igApplicationRepo}"){

                            sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build IG docker image...\""""

                            def dockerImage = docker.build("${repoName}/ig-temurin:v${BUILD_NUMBER}", ".")
                            def igImageName = dockerImage.id

                            sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Image ID: ${igImageName}\""""

                            docker.withRegistry('', "${registryCredential}") {
                                dockerImage.push()
                            }

                            sh "docker images"
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }

                }
            }
        }
    }
}
