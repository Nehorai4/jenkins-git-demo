pipeline {
    agent any
    environment {
        MY_NAME = 'Nehorai'
        IS_DEBUG = 'true'
    }
    stages {
        stage('Say Hello') {
            steps {
                script {
                    if (env.IS_DEBUG == 'true') {
                        echo "Hello, ${env.MY_NAME}! Debug mode is ON."
                    } else {
                        echo "Hello, ${env.MY_NAME}! Debug mode is OFF."
                    }
                }
            }
        }
        stage('Get Last Commit') {
            steps {
                script {
                    def commitMsg = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                    echo "Last commit message: ${commitMsg}"
                }
            }
        }
        stage('Approve') {
            steps {
                input message: "Do you want to continue, ${env.MY_NAME}?", ok: 'Yes'
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