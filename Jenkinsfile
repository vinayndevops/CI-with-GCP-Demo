pipeline {
    agent any
    environment {
       PROJECT_ID = 'DevOps'
       CLUSTER_NAME = 'k8s-cluster'
       LOCATION = 'us-east1-b'
       CREDENTIALS_ID = 'k8sengine'
    } 	
       stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        stage('Build package') {
            steps {
                echo "Building.."
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
		   appimage = docker.build("vinaydevops/devops:${env.BUILD_ID}")
                   docker.withRegistry('https://registry.hub.docker.com','dockerhub'){
                     appimage.push()
		   }
		}
            }
        }
        stage('Deploy to K8s') {
            steps {
                echo 'Deploying to K8s Cluster'
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clustername: env.CLUSTER_NAME, location: env.LOCATION, credentialsId: env.CREDENTIALS_ID, verifyDeployments: false])
                echo "Deployment Finished"
            }
        }
    }
}
