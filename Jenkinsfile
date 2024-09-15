pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
		//docker 'Docker'
	}
	environment {
		DOCKER_CREDENTIALS_ID = 'dockerhub-cred'
		DOCKER_REGISTRY = 'https://hub.docker.com/u/iquantc'
	}
	stages {
		stage('Checkout Github'){
			steps {
				git branch: 'main', credentialsId: 'jen-doc-git', url: 'https://github.com/iQuantC/NodeApp.git'	
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
					docker.build("nodeimage"+"$BUILD_NUMBER")
				}	
			}
		}
		stage('Push Image to DockerHub'){
			steps {
				script {
					withDockerRegistry(credentialsId: 'dockerhub-cred', toolName: 'Docker', url: 'DOCKER_REGISTRY') {
    						docker.image.push([latest])
					}
				}
			}
		}
	}

	post {
		success {
			echo 'Build completed succesfully!'
		}
		failure {
			echo 'Build failed. Check logs.'
		}
	}
}
