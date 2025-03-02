pipeline {
    agent any

    environment {
        PYTHON_VERSIONS = '3.8 3.9'  // Array של גרסאות Python לבדיקה ובנייה
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')  // DockerHub credentials מתוך Jenkins
        REPO_NAME = 'nehorai4/python-faker'  // שם ה-Repository שלך ב-DockerHub (תתאים לפי השם שלך)
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Nehorai4/jenkins-git-demo.git'  // Repository שלך
            }
        }

        stage('Install Dependencies') {
            agent {
                docker {
                    image 'python:3.9'  // תמונת Python בסיסית להתקנת תלויות
                    args '-v $HOME/.cache/pip:/root/.cache/pip'  // שימוש ב-cache של pip
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
                        values '3.8', '3.9'  // גרסאות Python לבדיקה
                    }
                }
                stages {
                    stage('Run Test') {
                        agent {
                            docker {
                                image "python:${PYTHON_VERSION}"  // גרסה דינמית מה-matrix
                                reuseNode true  // שימוש חוזר ב-Workspace
                            }
                        }
                        steps {
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
                        values '3.8', '3.9'  // גרסאות Python לבנייה
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
                        values '3.8', '3.9'  // גרסאות Python לדחיפה
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
            cleanWs()  // ניקוי ה-Workspace בסוף
        }
    }
}