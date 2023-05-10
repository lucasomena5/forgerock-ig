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
        
        stage('Build Base Image') {
            steps {
                script {
                    try {

                        def baseImageRepo = "${env.WORKSPACE}/identity-gateway/ig-baseimage"
                        def igApplicationRepo = "${env.WORKSPACE}/identity-gateway/ig-application"

                        sh "cat ${baseImageRepo}/Dockerfile"

                        dir("${baseImageRepo}"){

                            sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building IG base docker image...\""""

                            def dockerBaseImage = docker.build("${repoName}/forgerock-temurin:11", ".")
                            def baseImageName = dockerBaseImage.id

                            sh "echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` IG Base Image ID: ${baseImageName}\""""

                            // docker.withRegistry('', "${registryCredential}") {
                            //     dockerBaseImage.push()
                            // }
                        }

                        dir("${igApplicationRepo}"){
                            sh """sed -i 's/__BASEIMAGE_NAME__/${baseImageName}/g' Dockerfile"""
                            sh "cat ${igApplicationRepo}/Dockerfile"
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
                        def igApplicationRepo = "${env.WORKSPACE}/identity-gateway/ig-application"

                        dir("${igApplicationRepo}"){

                            sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build IG docker image...\""""

                            def dockerImage = docker.build("${repoName}/ig:v${BUILD_NUMBER}", ".")
                            def igImageName = dockerImage.id

                            sh "echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` IG Application Image ID: ${igImageName}\""""

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
