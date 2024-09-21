pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-jenkins-token'
		DOCKER_HUB_REPO = 'iquantc/iquant-app'
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
					dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
				}
			}
		}
		stage('Push Image to DockerHub'){
			steps {
				script {
					docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}"){
						dockerImage.push('latest')
					}
				}
			}
		}
		stage('Install kubectl'){
			steps {
				script {
					sh "apt-get update -y"
					sh "apt-get install -y apt-transport-https ca-certificates curl gnupg"
					sh "curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg"
					sh "chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg"
					sh "echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | tee /etc/apt/sources.list.d/kubernetes.list"
					sh "chmod 644 /etc/apt/sources.list.d/kubernetes.list"
					sh "apt-get update -y"
					sh "apt-get install -y kubectl" 
					sh "kubectl version --client"
				}
			}
		}
		stage('Deploy to Kubernetes'){
			steps {
				kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCakNDQWU2Z0F3SUJBZ0lCQVRBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwdGFXNXAKYTNWaVpVTkJNQjRYRFRJME1Ea3dOekU1TXpjMU5sb1hEVE0wTURrd05qRTVNemMxTmxvd0ZURVRNQkVHQTFVRQpBeE1LYldsdWFXdDFZbVZEUVRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTkVrCnJ1RTFqZCs0NC8rYkJPZEZpT1oyUWhzYklHSzVLVG1Talc4WEZ4V201NTQ3QXZYemNxU1dnSE5VSTllNjJ3N1UKcUxMREhXWDhPYmFuUDlKUjF4VkNNWWQ0cVBxcXRWS0kwT3h5bkdTNmRzV05La2FMVldJdVc3b0dIeXJBTnVwMAo3bEF6Q3RnWmZPU3N4cDNBL3grVGpYbWpGNlplWkdxdUoxOVJCZ1N3ZFdWc1NBQ0VHM3RZRnFKd1JEL1Z3bzV1CndyT1ZTdUs5M1ZNUDh1REhveDZ6RkZ5Yk5TdW1xSEptcWRIUUFvb0lleWJjVDNTQ1RaRkttQjJzNmV6NnZRM0IKZVhBaTQwQ3NoZjlUMzhTYlVvRXNtb0FQdktHQURyc2RDNUVpZ0JJdWNKTzBtSjZTblk3THZ2Q2hIKzhPR1orNAp3bW1XcGt6TkpnM0xkZk9RRmkwQ0F3RUFBYU5oTUY4d0RnWURWUjBQQVFIL0JBUURBZ0trTUIwR0ExVWRKUVFXCk1CUUdDQ3NHQVFVRkJ3TUNCZ2dyQmdFRkJRY0RBVEFQQmdOVkhSTUJBZjhFQlRBREFRSC9NQjBHQTFVZERnUVcKQkJRaVFkbFFIRUhtUklTZFQ2cmF4MFZ0cGpBOGJUQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFZMjRzRXpmTQpZZHl4Szd6bjc4VklYMUFNK0xDVHQyOTFPb3Roa1loajFxYXJJMzZSMEhYVXZRa3FaYVFSUXhxQWZsMnhSaVd2CkY1VUlJbm9YcmVxTFdhNVk4emh0dFJxK05rZU9iaXV4MzhIb1FWWTl4dG5BcFh2TE9RanF4LzZhcVlzdjJpNUgKTmpJMWQxdWVWMmJGVHZWODJlRDdxNWNabXpPaFJxcWNPUVBvYkZPNW9qbXpLTVh1ajZ3VlpWNlk4cmdzTlFOeQpuZU00NEg0NmU5OW9hZTlsMlZUM3BEVE9IeUVIYmhFUXBFaHE5RXpBVUZMQUNRa01kN1VGaENacXhmOHRZRG85CndzaXFCem5lYUVUYUZtRlZFQTEwUk5uNjhtV05rT09XZEVhNmNGNVhNTUQrRjMwaGs4eXV4OVlzcG9uaHF5d0sKUHZ5T0Vocm9EVjUyd1E9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubeconfig', serverUrl: 'https://192.168.49.2:8443') {
 
					sh 'kubectl apply -f deployment.yaml'
					sh 'kubectl apply -f service.yaml'
				}
			}
		}
	}

	post {
		success {
			echo 'Build&Deploy completed succesfully!'
		}
		failure {
			echo 'Build&Deploy failed. Check logs.'
		}
	}
}
