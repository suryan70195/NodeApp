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
