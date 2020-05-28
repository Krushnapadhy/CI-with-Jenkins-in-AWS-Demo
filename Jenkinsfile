pipeline {
	agent any
	environment {
			PROJECT_ID = 'carbon-crossing-267321'
	                CLUSTER_NAME = 'kp-kubernetes'
	                LOCATION = 'europe-north1-a'
	                CREDENTIALS_ID = 'K8'
	}
	stages {
		stage('Checkout Project Code') {
			steps {
				checkout scm
			}
		}
		stage('Build') {
			steps {
				echo "Cleaning and packaging"
				sh 'mvn clean package'
			}
		}
		stage('Test') {
			steps {
				echo "Testing"
				sh 'mvn test'
			}
		}
		stage('Build Docker Image') {
			steps {
				script 
				{
					myimage = docker.build("eu.gcr.io/carbon-crossing-267321/krushnapadhy/demogcr:${env.BUILD_ID}")
					
				}
			}
		}
		stage("Push Docker Image") {
			steps {
				script {
					docker.withRegistry('https://eu.gcr.io') 
					{
						myimage.push("${env.BUILD_ID}")
					}
				}
			}
		}
		stage('Deploy to K8s') {
			steps{
				echo "Deployment started ..."
				sh 'ls -ltr'
				sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				echo "Deployment Finished ..."
			}
		}
	}
}