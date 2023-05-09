pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
    }
    
    stages {
        stage('Authenticate with Docker Hub') {
		    steps {

				sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Checking pre-requisites...\""""
				
                def osName = sh '''cat /etc/*release* | grep DISTRIB_ID | cut -d '=' -f 2'''
                sh "echo ${env.osName}"
                
                if (env.osName == 'Ubuntu') {
                    
                    def isPackageInstalled = true
                    if (env.isPackageInstalled){
                        sh '''aws --version'''
                    }
                    
                } else {

                    def isPackageInstalled = true
                    if (env.isPackageInstalled){
                        sh '''aws --version'''
                    }
                }    

                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''docker login -u $USERNAME -p $PASSWORD'''
                }
		    }
		}
        
        // stage('Build Docker Image') {
        //     steps {
        //         sh 'echo "Building Docker image"'
        //         sh 'docker build . -t ig:v2023.4.0'
        //     }
        // }
    }
}
