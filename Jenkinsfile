pipeline {
    agent any
    environment {
        MY_NAME = 'Nehorai'
        IS_DEBUG = 'true'  // משתנה שקובע אם אנחנו במצב דיבאג
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
        stage('Create File') {
            steps {
                sh 'echo "File created by ${MY_NAME}" > demo.txt'
                archiveArtifacts artifacts: 'demo.txt', allowEmptyArchive: true
            }
        }
    }
}