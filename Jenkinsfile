pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image'){
            when{
                branch 'master'
            }
            steps{
                script{
                    app=docker.build("kamranch24/train-schedule")
                   
                    }    
            }
                  
        }
        stage('Push Docker Image'){
            when{
                branch 'master'
                }
            steps{
                script{
                        docker.withRegistry('https://registry.hub.docker.com/','DockerCred'){
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                        }
                    
                }
                    
            }
        }
         stage('Deploy to Production'){
            when{
                branch 'master'
                }
            steps{
                input 'Deploy to production'
                milestone(1)
                withCredentials([usernamePassword(credentialsId:'web-server-cred', usernameVariable:'USERNAME', passwordVariable:'USERPASS' )]){
                script{
                    sh 'docker pull kamranch24/train-schedule:${env.BUILD_NUMBER}'
                    sh 'docker stop train-schedule'
                    sh 'docker rm train-schedule'
                    sh 'docker run --restart always --name train-schedule -p 8080:8080 -d kamranch24/train-schedule:${env.BUILD_NUMBER}'
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                        }
                    
                }
                }
                    
            }
        }
       
    }
}
