properties(
	[
		buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')), 
        disableConcurrentBuilds()
	]
)

pipeline {
    agent any
    parameters {
        string(name: 'RepoName', defaultValue: '', description: 'Artifact Repository')
        choice(
            name: 'RebuildBaseImage',
            choices: ['yes', 'no'],
            description: "Choose 'no' to skip build Base Image step"
        )
    }
	environment {
        dockerCredential = credentials('docker-hub-credentials')
        gitHubCredential = credentials('jenkins_prudential_key')
        registryCredential = 'docker-hub-credentials'
        baseImageRepo = "${env.WORKSPACE}/identity-gateway/ig-baseimage"
        igApplicationRepo = "${env.WORKSPACE}/identity-gateway/ig-application"
        baseImageName = "${repoName}/forgerock-temurin:11"
    }
    
    stages {

        stage('Initialization') {
            steps {
                script {
                    export repoName="${params.PARAM_NAME}"
					
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
                expression {
                    params.RebuildBaseImage == 'yes'
                }
            }
            steps {
                script {
                    try {

                        echo "cat ${baseImageRepo}/Dockerfile"

                        dir("${baseImageRepo}"){

                            echo """\"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building IG base docker image...\""""

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

                            echo """\"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build IG docker image...\""""

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
    }
}
