pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
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
                sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build docker image...\""""
                
                sh "ls -lah"
                
                //sh 'docker build . -t ig:v2023.4.0'
            }
        }
    }
}
