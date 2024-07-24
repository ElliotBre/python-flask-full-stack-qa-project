pipeline {
    agent { label 'docker-agent-1'}
    environment {
        DOCKER_ID = credentials('DOCKER_ID')
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
    }
        stages {
            stage ("Setup") {
                steps {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
                    checkout scmGit(
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'git@github.com:ElliotBre/qa_project.git']])
                        sh "touch env.sh"
                        withCredentials([file(credentialsId: 'env.sh', variable: 'ENV')]){
                            sh "cp \$ENV env.sh"
                        }
                }
             }
             stage ("Build") {
                steps {
                    sh "git submodule init"
                    sh "git submodule update"
                    sh  "docker build -t mmbatteries/db:latest ./postgres_db"
                    sh "docker build -t mmbatteries/app:latest ./flask_app"
                }
             }
             stage ("Run") {
                steps {
                    script {
                       load './env.sh'
                    }
                    
                    sh "docker network create --driver=bridge testing"
                    sh "docker run -d -p 5432:5432 --mount type=volume,source=db,target=/var/lib/postgres --hostname=db --name=db --network=testing -e POSTGRES_USER=$DATABASE_USER -e POSTGRES_PASSWORD=$DATABASE_PASSWORD -e POSTGRES_NAME=$DATABASE_NAME mmbatteries/db:latest"
                    sh "docker run -d -p 5000:5000 --mount type=bind,source=./flask_app,target=/app/ --restart=always --hostname=app --name=app --network=testing -e DATABASE_NAME=$DATABASE_NAME -e HOST=$DATABASE_HOST -e DATABASE_USER=$DATABASE_USER -e DATABASE_PASSWORD=$DATABASE_PASSWORD -e DATABASE+HOST=$DATABASE_HOST -e FLASK_ENV=$FLASK_ENV -e FLASK_APP=$FLASK_APP -e FLASK_DEBUG=$FLASK_DEBUG -e DATABASE_PORT=$DATABASE_PORT -e PORT=$PORT mmbatteries/app:latest"
                    sh "docker run -d -p 80:80 --mount type=bind,source=./nginx.conf,target=/etc/nginx/nginx.conf --network=testing --name=nginx nginx:latest"

                }
             }
            stage ("Archive build artifacts") {
                steps {
                    sh "docker push mmbatteries/db:latest"
                    sh "docker push mmbatteries/app:latest"
                    sh "cd .."
                    sh "rm -rf temp"
                }
            }
            stage ("Running minikube") {
                steps {
                    sh "kubectl apply -f app-service.yaml,db-service.yaml,nginx-service.yaml,app-deployment.yaml,db-deployment.yaml,app-cm0-configmap.yaml,env-configmap.yaml,db-persistentvolumeclaim.yaml,nginx-deployment.yaml,nginx-cm0-configmap.yaml"
                }
            }
        }
    post {
      always {
          sh "rm env.sh"
          sh "docker stop \$(docker ps -aq) && docker remove \$(docker ps -aq)"
          sh "docker network rm testing"
          sh "docker system prune -f"
      }
    }
}
