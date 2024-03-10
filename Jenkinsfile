node {
    stage ("Create build output") {

        sh "mkdir -p output"

        writefile file: "output/test.txt", text: "this file is a test"
    }

    stage ("Archive build artifacts") {
        archiveArtifacts artifacts: 'output/*.txt'
    }
}