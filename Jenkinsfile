pipeline{
	agent any
	
	stages{
		stage('Checkout'){
			steps{
				git credentialsId: 'personalGit', url: 'https://github.com/lucasomena5/personal-jenkins-lab.git'
			}
		}
    		stage('Setup'){
      			steps{
				sh 'echo \'test phase\''
      			}
    		}
    		stage('Test'){
      			steps{
				 sh 'echo \'test phase\''
               }
   		}
		stage('invoke playbook'){
      			steps{
				ansiblePlaybook installation: 'ansible-2.9.25', playbook: './ansible/1-playbook.yaml'               			
      			}
   		}
	}
}