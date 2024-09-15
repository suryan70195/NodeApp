pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
		//docker 'Docker'
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
		stage('Push Image to Dockerhub'){
			steps {
				script {
					//withDockerRegistry(credentialsId: 'dockerhub-jenkins-token') {
					//	docker.image.push(['latest'])
					withDockerRegistry(credentialsId: 'dockerhub-jenkins-token', toolName: 'Docker') {
						docker.image.push('latest')
						//}
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
