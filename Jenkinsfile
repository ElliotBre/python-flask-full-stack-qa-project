pipeline {
    agent { 
        docker { image 'docker:latest' 
        } 
    }
        stages {
            stage ("Setup") {
                steps {
                    sh "rm -rf qa_project"
                    sh "ls -a"
                    checkout scmGit(
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'git@github.com:ElliotBre/qa_project.git']])
                        sh "ls -a"
                        sh "touch .env"
                        withCredentials([file(credentialsId: '.env', variable: 'ENV')]){
                            sh "cp \$ENV .env"
                        }
                        sh "ls -a"
                }
             }
             stage ("Build") {
                steps {
                
                // sh "docker-compose up"
                  sh 'sleep 10s'
                }
             }
            
            stage ("Archive build artifacts") {
                steps {
                //    sh 'docker image push mmbatteries/db:1.5.0'
                //    sh 'docker image push mmbatteries/app:1.'
                  sh 'sleep 43s'
                }
            }
        }
}



