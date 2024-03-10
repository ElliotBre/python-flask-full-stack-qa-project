node {
    stage ("Clone git repo") {
        sh "git clone git@github.com:ElliotBre/qa_project.git"
    }
    stage ("Startup app"){
        sh "docker compose up"
    }
    stage ("Create build output") {

        sh "mkdir -p output"

        writeFile file: "output/test.txt", text: "this file is a test"
    }
    stage ("Archive build artifacts") {
        archiveArtifacts artifacts: 'output/*.txt'
    }
}