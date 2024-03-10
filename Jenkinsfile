node {
    stage ("Startup app") {
        sh "rm -rf qa_project"
        sh "touch .env"
        withCredentials([file(credentialsId: '.env', variable: 'ENV')]){
                            sh "cp \$ENV .env"
        }
        sh "git clone git@github.com:ElliotBre/qa_project.git; mv .env qa_project; cd qa_project; ls -a; docker compose down; docker compose up"
    }
    stage ("Create build output") {

        sh "mkdir -p output"

        writeFile file: "output/test.txt", text: "this file is a test"
    }
    stage ("Archive build artifacts") {
        archiveArtifacts artifacts: 'output/*.txt'
    }
}