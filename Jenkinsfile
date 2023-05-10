properties(
	[
		buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')), 
        disableConcurrentBuilds()
	]
)

pipeline {
    agent any
    parameters {
        string(name: 'repoName', defaultValue: '', description: 'i.e. devforge1')
        string(name: 'baseImageName', defaultValue: '', description: 'i.e. forgerock-temurin:11')
        booleanParam(
            name: 'RebuildBaseImage',
            defaultValue: false,
            description: "Whether to rebuild the base image"
        )
        // choice(
        //     name: '',
        //     choices: ['yes', 'no'],
        //     description: "Choose 'no' to skip build Base Image step"
        // )
    }
	environment {
        dockerCredential = credentials('docker-hub-credentials')
        gitHubCredential = credentials('jenkins_prudential_key')
        registryCredential = 'docker-hub-credentials'
        baseImageRepo = "${env.WORKSPACE}/identity-gateway/ig-baseimage"
        igApplicationRepo = "${env.WORKSPACE}/identity-gateway/ig-application"
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
                    echo """\"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Checking pre-requisites...\""""
			     
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''docker login -u $USERNAME -p $PASSWORD'''
                    }
                }
                
		    }
		}
        
        stage('Build Base Image') {
            when {
                expression { params.RebuildBaseImage == true }
            }
            steps {
                script {
                    try {

                        echo "cat ${baseImageRepo}/Dockerfile"

                        dir("${baseImageRepo}"){

                            echo """\"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building IG base docker image...\""""

                            def dockerBaseImage = docker.build("${repoName}/${baseImageName}", ".")

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

                            echo """\"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build IG docker image...\""""

                            if (params.RebuildBaseImage == false) {
                                sh """sed -i "s/\# FROM devforge1/forgerock-temurin:11/FROM devforge1\/forgerock-temurin:11/g\" Dockerfile"""
                            } else {
                                sh """sed -i "s/\# FROM __BASEIMAGE_NAME__/FROM ${repoName}\/${baseImageName}/g" Dockerfile"""
                            }
                            
                            def dockerImage = docker.build("${repoName}/ig-temurin:v${BUILD_NUMBER}", ".")
                            
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

        stage('Remove Unused Images') {
            steps {
                script {
                    try {
                        sh "docker system prune --force --all"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }

                }
            }
        }
    }
}
