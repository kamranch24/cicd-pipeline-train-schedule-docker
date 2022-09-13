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
        stage{
            when{
                branch 'master'
            }
            steps{
                script{
                    app=docker.build("kamranch24/train-schedule")
                    app.inside{
                        sh 'echo ${curl localhost:8080}'
                         }
                    }    
            }
                  
        }
        stage{
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
    }
}
