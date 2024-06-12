pipeline {
    agent { 
         docker { image 'docker:latest' 
        } 
    }
        stages {
            stage ("Setup") {
                environment {
                HOME = "${env.WORKSPACE}"
                }
                steps {
                    sh "rm -rf qa_project"
                    sh "ls -a"
                    checkout scmGit(
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'git@github.com:ElliotBre/qa_project.git']])
                        sh "ls -a"
                        sh "touch .env"
                        withCredentials([file(credentialsId: '.env', variable: 'ENV')]){
                            sh "echo \$ENV"
                            sh "echo \$ENV >> .env"
                        }
                        sh "ls -a"
                }
             }
             stage ("Build") {
                environment {
                HOME = "${env.WORKSPACE}"
                }
                steps {
                    sh "sudo systemctl start docker"
                    sh  "docker build -t mmbatteries/db:latest ./postgres_db"
                    sh "docker build -t mmbatteries/app:latest ./flask_app"
                }
             }
             stage ("Run") {
                environment {
                HOME = "${env.WORKSPACE}"
                }
                steps {

                    sh "set -a"
                    sh "source .env"
                    sh "set +a"
                    
                    sh "docker network create —driver=bridge testing"
                    sh "docker run -p 5432:5432 --mount type=volume,source=db,target=/var/lib/postgres --hostname=db --name=db --network=testing -e POSTGRES_USER=$DATABASE_USER -e POSTGRES_PASSWORD=$DATABASE_PASSWORD -e POSTGRES_NAME=$DATABASE_NAME mmbatteries/db:latest"
                    sh "docker run -p 5000:5000 --mount type=bind,source=./flask_app,target=/app/ --restart=always --hostname=app --name=app --network=testing -e DATABASE_NAME=$DATABASE_NAME -e HOST=$DATABASE_HOST -e DATABASE_USER=$DATABASE_USER -e DATABASE_PASSWORD=$DATABASE_PASSWORD -e DATABASE+HOST=$DATABASE_HOST -e FLASK_ENV=$FLASK_ENV -e FLASK_APP=$FLASK_APP -e FLASK_DEBUG=$FLASK_DEBUG -e DATABASE_PORT=$DATABASE_PORT -e PORT=$PORT mmbatteries/app:latest"
                    sh "docker run -p 80:80 --mount type=bind,source=./nginx.conf,target=/etc/nginx/nginx.conf --network=network --name=nginx nginx:latest"

                }
             }
            stage ("Archive build artifacts") {
                environment {
                HOME = "${env.WORKSPACE}"
                }
                steps {
                    sh "docker push mmbatteries/db:latest"
                    sh "docker push mmbatteries/app:latest"
                }
            }
        }
}
