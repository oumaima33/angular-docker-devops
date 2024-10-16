pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-op')
        DOCKER_TEST_IMAGE = "awwin/new-angular:test"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t awwin/new-angular:${env.BUILD_NUMBER} ."
                }
            }
        }

       

        stage('Security Scan with Grype') {
            steps {
                script {
                    // Run the Grype security scan on the built Docker image
                    bat "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock anchore/grype awwin/new-angular:${env.BUILD_NUMBER}"
                }
            }
        }
        
         stage('Login to DockerHub') {
          steps {
           script {
            // Replace "awwin" with your DockerHub username and "dckr_pat_GNPpm2o6qaOuxZfh7Y0S7pMpqZ0" with your actual password.
            bat "echo ${DOCKERHUB_CREDENTIALS_PSW}"
            bat "echo ${DOCKERHUB_CREDENTIALS_USR}"
            // Use the echo command to pass the password to docker login.
            bat "docker login -u ${DOCKERHUB_CREDENTIALS_USR} -p ${DOCKERHUB_CREDENTIALS_PSW}"
            }
          }
      }
        
        stage('Push to DockerHub') {
            steps {
                script {
                    bat "docker push awwin/new-angular:${env.BUILD_NUMBER}"
                 
                }
            }
        }
        
        stage('Deploy back end') {
            steps {
                script {
                   
                     bat "docker run -d -p 8081:80 test-back-conc:test"
                }
            }
        }

         stage('Deploy front end') {
            steps {
                script {
                    bat "docker run -d -p 4201:80 awwin/new-angular:${env.BUILD_NUMBER}"
                   
                }
            }
        }
      
    }
    
    post {
        always {
            bat "docker logout"
        }
    }
}
