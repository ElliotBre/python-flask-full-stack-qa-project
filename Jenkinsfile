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
                    sh  "docker build -t mmbatteries/db:latest ./postgres_db"
                    sh "docker build -t mmbatteries/app:latest ./flask_app"
                }
             }
             stage ("Run") {
                steps {

                    sh "set -a
                        . ./.env
                        set +a"

                    sh "docker run -p 5432:5432 --mount type=bind,source=db,target=/var/lib/postgres -e POSTGRES_USER=$DATABASE_USER -e POSTGRES_PASSWORD=$DATABASE_PASSWORD -e POSTGRES_NAME=$DATABASE_NAME"
                    sh "docker run -p 5000:5000 —restart=always -e DATABASE_NAME=$DATABASE_NAME -e HOST=$DATABASE_HOST -e DATABASE_USER=$DATABASE_USER -e DATABASE_PASSWORD=$DATABASE_PASSWORD -e DATABASE_HOST=$DATABASE_HOST -e FLASK_ENV=$FLASK_ENV -e FLASK_APP=$FLASK_APP -e FLASK_DEBUG=$FLASK_DEBUG -e DATABASE_PORT=$DATABASE_PORT -e PORT=$PORT —mount type=bind,source=./flask_app,target=/app/"
                    sh "docker run -p 80:80 --mount type=bind,source=./nginx.conf,target=/etc/nginx/nginx.conf nginx:latest"

                }
             }
            stage ("Archive build artifacts") {
                steps {
                    sh "docker push mmbatteries/db:latest"
                    sh "docker push mmbatteries/app:latest"
                }
            }
        }
}



