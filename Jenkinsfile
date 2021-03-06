pipeline {
    agent any
    environment {
       PROJECT_ID = 'devops-260921'
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
		   appimage = docker.build("gcr.io/devops-260921/vinzdevops/devops:${env.BUILD_ID}")
                  //docker.withRegistry('https://registry.hub.docker.com','dockerhub'){
                   docker.withRegistry('https://gcr.io','gcr:gcr_cred'){
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
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                echo "Deployment Finished"
            }
        }
    }
}
