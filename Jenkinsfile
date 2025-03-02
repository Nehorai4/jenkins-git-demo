pipeline {
    agent any

    environment {
        PYTHON_VERSIONS = '3.8 3.9'
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')  // נשאר אותו דבר
        REPO_NAME = 'nehorai4/python-faker'
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Nehorai4/jenkins-git-demo.git'
            }
        }

        stage('Install Dependencies') {
            agent {
                docker {
                    image 'python:3.9'
                    args '-v $HOME/.cache/pip:/root/.cache/pip'
                }
            }
            steps {
                sh 'pip install faker'
            }
        }

        stage('Test Application') {
            matrix {
                axes {
                    axis {
                        name 'PYTHON_VERSION'
                        values '3.8', '3.9'
                    }
                }
                stages {
                    stage('Run Test') {
                        agent {
                            docker {
                                image "python:${PYTHON_VERSION}"
                                reuseNode true
                            }
                        }
                        steps {
                            sh 'pip install faker'
                            sh 'python app.py'
                        }
                    }
                }
            }
        }

        stage('Build Docker Images') {
            matrix {
                axes {
                    axis {
                        name 'PYTHON_VERSION'
                        values '3.8', '3.9'
                    }
                }
                stages {
                    stage('Build Image') {
                        steps {
                            script {
                                def image = docker.build("${REPO_NAME}:${PYTHON_VERSION}", "--build-arg PYTHON_VERSION=${PYTHON_VERSION} .")
                            }
                        }
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            matrix {
                axes {
                    axis {
                        name 'PYTHON_VERSION'
                        values '3.8', '3.9'
                    }
                }
                stages {
                    stage('Push Image') {
                        steps {
                            script {
                                docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {  // שינוי ל-ID ישיר
                                    docker.image("${REPO_NAME}:${PYTHON_VERSION}").push()
                                }
                            }
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            deleteDir()
        }
    }
}