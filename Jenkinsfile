pipeline {
    agent any
    environment {
        MY_NAME = 'Nehorai'
    }
    stages {
        stage('Say Hello') {
            steps {
                echo "Hello, ${env.MY_NAME} from Git!"
            }
        }
        stage('Create File') {
            steps {
                sh 'echo "File created by ${MY_NAME}" > demo.txt'
                archiveArtifacts artifacts: 'demo.txt', allowEmptyArchive: true
            }
        }
    }
}