pipeline {
         agent {
        docker {
          image 'node:carbon'
        }
      }
    stages {
        stage('checkout'){
            steps{
                git branch: 'master', url: 'https://github.com/kamranch24/cicd-pipeline-train-schedule-docker.git'
            }
        }
        stage('Build') {
       
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image'){
            
            
            steps{
                script{
                    app=docker.build("kamranch24/train-schedule")
                   
                    }    
            }
                  
        }
         stage('Push Docker Image') {
              
            
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DockerCred') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            
           
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'web-server-cred', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod \"docker pull kamranch24/train-schedule:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod \"docker stop train-schedule\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod \"docker rm train-schedule\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod \"docker run --restart always --name train-schedule -p 8080:8080 -d kamranch24/train-schedule:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
                    
     } 
}
    
                    
