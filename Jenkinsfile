 pipeline {
     options {
         buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
     }

     agent any

     tools {
         maven 'maven_3.9.4'
     }

     stages {
         stage('Code Compilation') {
             steps {
                 echo 'Code Compilation is In Progress!'
                 sh 'mvn clean compile'
                 echo 'Code Compilation is Completed Successfully!'
             }
         }

         stage('Code Package') {
             steps {
                 echo 'Creating War Artifact'
                 sh 'mvn clean package'
                 echo 'Creating War Artifact done'
             }
         }
         stage('Building & Tag Docker Image') {
             steps {
                 echo 'Starting Building Docker Image'
                 sh "docker build -t  bhalchandra20299/flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER} ."
                 sh "docker build -t flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER} ."
                 echo 'Completed Building Docker Image'
             }
         }
         stage('Docker Image Scanning') {
             steps {
                 echo 'Docker Image Scanning Started'
                 sh 'java -version'
                 echo 'Docker Image Scanning Started'
             }
         }
         stage('Docker push to Docker Hub') {
             steps {
                 script {
                     withCredentials([string(credentialsId: 'dockerhubCred', variable: 'dockerhubCred')]) {
                         sh "docker login docker.io -u  bhalchandra20299 -p ${dockerhubCred}"
                         echo "Push Docker Image to DockerHub: In Progress"
                         sh "docker push  bhalchandra20299/flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}"
                         echo "Push Docker Image to DockerHub: Completed"
                     }
                 }
             }
         }
         stage('Docker Image Push to Amazon ECR') {
             steps {
                 script {
                     withDockerRegistry([credentialsId: 'ecr:ap-south-1:ecr-credentials', url: "https://802127431620.dkr.ecr.ap-south-1.amazonaws.com"]) {
                         sh """
                         echo "Tagging the Docker Image: In Progress"
                         docker tag flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}  802127431620.dkr.ecr.ap-south-1.amazonaws.com/flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}
                         echo "Tagging the Docker Image: Completed"
                         echo "Push Docker Image to ECR: In Progress"
                         docker push 802127431620.dkr.ecr.ap-south-1.amazonaws.com/flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}
                         echo "Push Docker Image to ECR: Completed"
                          """
                     }
                 }
             }
         }
         stage(' upload the docker image to nexus') {
              steps {
                   script {
                          withCredentials([usernamePassword(credentialsId: 'nexusCred', usernameVariable: 'USERNAME ',passwordVariable:'PASSWORD')]) {
                          sh "docker login  http://3.6.37.183:8085/repository/flipkart-ms -u admin -p ${password}"
                          echo "Push Docker Image to nexus: In Progress"
                          sh "docker tag  flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}" 3.6.37.183:8085/flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}"
                          sh "docker push 3.6.37.183:8085/flipkart-ms:dev-flipkart-ms-v1.${BUILD_NUMBER}"
                          echo "Push Docker Image to nexus: Completed"
                          }
                   }
              }
         }
 	}
 }