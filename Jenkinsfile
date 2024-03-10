node {
    stage ("Startup app") {
        sh "rm -rf qa_project"
        sh "touch .env"
        withCredentials([file(credentialsId: '.env', variable: '.env'){
                            sh "cp \$.env .env"
        ])}
        sh "git clone git@github.com:ElliotBre/qa_project.git;ls -a; mv .env qa_project; cd qa_project; ls -a; docker compose up"
    }
    stage ("Create build output") {

        sh "mkdir -p output"

        writeFile file: "output/test.txt", text: "this file is a test"
    }
    stage ("Archive build artifacts") {
        archiveArtifacts artifacts: 'output/*.txt'
    }
}