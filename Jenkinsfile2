pipeline {
    agent any
        environment {
        //once you create ACR in Azure cloud, use that here
        registryName = "todotfacr"
        //- update your credentials ID after creating credentials for connecting to ACR
        registryCredential = 'todotfacr'
        dockerImage = 'todotfacrimg'
        registryUrl = 'todotfacr.azurecr.io'
    }
    
    stages {
        stage('checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ankit-antony-devops/mern-stack-todo.git']]])
            }
        }
     
    stage ('Build Docker image') {
        steps {
                script {
                    dockerImage = docker.build registryName
                }
            }
        }
        
    // Uploading Docker images into ACR
        stage('Upload Image to ACR') {
         steps{   
             script {
                docker.withRegistry( "http://${registryUrl}", registryCredential ) {
                dockerImage.push()
                }
            }
          }
        }
        
        stage ('K8S Deploy') {
          steps {
            script {
                withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                sh ('kubectl apply -f  manifest.yaml')
                }
            }
        }
     }
    }
}
