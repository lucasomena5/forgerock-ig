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
		// 		),
		// 		choice(
		// 			choices: 
		// 			[''], 
		// 			description: 'Select the application', 
		// 			name: 'APPLICATION'
		// 		),
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

        // stage('Git Checkout') {
        //     steps {
        //         script {
        //             git branch: "master",
        //                 url: 'git@github.com:lucasomena5/forgerock-ig.git'
        //         }
        //     }
        // }
        
        stage('Build Docker Image') {
            steps {
                script {

                    def applicationRepo = "${env.WORKSPACE}/identity-gateway/application"
                    def osRepo = "${env.WORKSPACE}/identity-gateway/os"

                    dir("${applicationRepo}"){

                        sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build IG docker image...\""""
                        def dockerImage = docker.build("${repoName}/ig:v${BUILD_NUMBER}", ".")

                        docker.withRegistry('', "${registryCredential}") {
                            dockerImage.push()
                        }
                        
                        

                    //sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Building application base image...\""""
                    
                    //sh """cat ${env.WORKSPACE}/identity-gateway/application/Dockerfile"""
                    //sh """ls -lha """
                        sh "docker images"
                    //sh """docker build ${env.WORKSPACE}/identity-gateway/application/Dockerfile -t ig:v${BUILD_NUMBER}"""
                    //sh "docker tag ig:v${BUILD_NUMBER} ${repoName}/ig:v${BUILD_NUMBER}"
                    //sh "docker ps -a"
                    //sh "docker push devforge1/ig:v${BUILD_NUMBER}"
                    }
                }
            }
        }
    }
}
