pipeline {
    agent any

    environment {
        PYTHON_VERSIONS = '3.8 3.9'  // רשימת גרסאות Python
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')  // Credentials ל-DockerHub
        REPO_NAME = 'nehorai4/python-faker'  // שם ה-Repository שלך ב-DockerHub
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
                            sh 'pip install faker'  // התקנת faker בכל גרסה
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
                                docker.withRegistry('https://index.docker.io/v1/', 'DOCKERHUB_CREDENTIALS') {
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
            deleteDir()  // ניקוי ה-Workspace בסוף
        }
    }
}