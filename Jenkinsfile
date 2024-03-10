node {
    stage ("Clone git repo") {
        sh "cd qa_project"
        sh "if ! [ \$? -eq 0 ]; then git clone git@github.com:ElliotBre/qa_project.git; fi"
    }
    stage ("Startup app"){
        sh "cd qa_project"
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