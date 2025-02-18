pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-jenkins-token'
		DOCKER_HUB_REGISTRY = 'https://hub.docker.com/r/kubersurya/nodejs'
                DOCKER_HUB_REPO = 'kubersurya/nodejs'
	}
	stages {
		stage('Checkout Github'){
			steps {
				git branch: 'main', credentialsId: 'suryachandu', url: 'https://github.com/suryan70195/NodeApp.git'
			}
		}		
		stage('Install node dependencies'){
			steps {
				sh 'npm install'
			}
		}
		stage('Test Code'){
			steps {
				sh 'npm test'
			}
		}
		stage('Build Docker Image'){
			steps {
				script {
				   docker.build("nodeimage"+"BUILD_NUMBER")
             			}
			}
		  }

	    }
	}	
}
