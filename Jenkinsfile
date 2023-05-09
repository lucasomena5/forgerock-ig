pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
    }
    
    stages {
        stage('Authenticate with Docker Hub') {
		    steps {
                script {
                    echo "[INFO] `date '+%Y-%m-%d %H:%M:%S'` Checking pre-requisites..."
			     
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''docker login -u $USERNAME -p $PASSWORD'''
                    }
                }
		    }
		}
        
        stage('Build Docker Image') {
            steps {
                script {
                    echo "[INFO] `date '+%Y-%m-%d %H:%M:%S'` Build docker image..."
                    ls -lah ${env.WORKSPACE}
                }    
            }
        }
    }
}
