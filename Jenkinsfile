pipeline {
    agent any 

     options {
        timeout(time: 10, unit: 'MINUTES')
     }
    environment {
    ACR_NAME = "todotfacr"
    registyUrl = "todotfacr.azurecr.io"
    IMAGE_NAME = "todotfacrimg"
    IMAGE_TAG = "latest"
    registryCredential  = "todotfacr"
    }
    stages { 
        stage('SCM Checkout') {
            steps{
           git branch: 'main', url: 'https://github.com/ankit-antony-devops/mern-stack-todo.git'
            }
        }
        // run sonarqube test
        /*stage('Run Sonarqube') {
            environment {
                scannerHome = tool 'ibt-sonarqube';
            }
            steps {
              withSonarQubeEnv(credentialsId: 'ibt-sonar', installationName: 'IBT sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner"
              }
            }
        }*/
       // Building Docker Image 
       stage ('Build Docker image') {
        steps {
                script {
                    //dockerImage = docker.build registryUrl
                 def dockerImage = docker.build("${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}", '.') 
                }
            }
       } 
    // Uploading Docker images into ACR
        stage('Upload Image to ACR') {
         steps{   
             script {
                 docker.withRegistry( "http://${ACR_NAME}.azurecr.io", registryCredential ) {
               // dockerImage.push()
              bat "docker push ${ACR_NAME}.azurecr.io/${IMAGE_NAME}:${IMAGE_TAG}"
                  }
              }
         }
      }

        stage('K8S Deploy') {
            steps {
                script {
                    app = docker.image('todotfacr.azurecr.io/todotfacrimg:latest')
                    withDockerRegistry([credentialsId: 'todotfacr', url: 'https://todotfacr.azurecr.io']) {
                    app.pull()
                    bat "kubectl create -f ."

          }
        }
      }
    }
 }
}
