node {
    stage ("Startup app") {
        sh "rm -rf qa_project"
        sh "touch .env"
        withCredentials([file(credentialsId: '.env', variable: 'ENV')]){
                            sh "cp \$ENV .env"
        }
        sh "ls -a"
        checkout scmGit(
            branches: [[name: 'main']],
            userRemoteConfigs: [[url: 'git@github.com:ElliotBre/qa_project.git']])
        sh "ls -a"
    }
    stage ("Create build output") {

        sh "mkdir -p output"

        writeFile file: "output/test.txt", text: "this file is a test"
    }
    stage ("Archive build artifacts") {
        archiveArtifacts artifacts: 'output/*.txt'
    }
}