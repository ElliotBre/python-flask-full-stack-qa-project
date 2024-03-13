node {
    stage ("Setup") {
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
    stage ("build") {
        docker.build()
    }
    stage ("Create build output") {

        sh "mkdir -p output"

        writeFile file: "output/test.txt", text: "this file is a test"
    }
    stage ("Archive build artifacts") {
        archiveArtifacts artifacts: 'output/*.txt'
    }
}