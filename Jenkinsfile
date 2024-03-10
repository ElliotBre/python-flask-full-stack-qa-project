node {
    stage ("Clone git repo") {
        sh "rm -rf qa_project"
        sh "git clone git@github.com:ElliotBre/qa_project.git; ls -a"
        sh "ls -a"
    }
    stage ("Startup app"){
        sh "ls -a"
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