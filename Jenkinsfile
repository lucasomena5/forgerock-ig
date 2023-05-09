pipeline {
    agent any
	environment {
        dockerCredential = credentials('docker-hub-credentials')
    }
    
    stages {
        stage('Authenticate with Docker Hub') {
		    steps {

				// sh """echo \"[INFO] `date '+%Y-%m-%d %H:%M:%S'` Install pre-requisites...\""""
				
				// if (env.BRANCH_NAME == 'master') {
                    
				// 	sudo apt-get install -y docker-cli 
                // } else {
                //     sudo yum update -y
                //     sudo yum install -y docker-cli
                // }

				withDockerRegistry([url: '', credentialsId: 'docker-hub-credentials']) {
                    sh 'docker push nginx'
                }
		        // withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
		        //     sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
		        // }
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
