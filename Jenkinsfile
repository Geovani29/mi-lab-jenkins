pipeline {
    agent any

    tools {
        nodejs 'node'
    }
    environment {
        IMAGE_NAME = 'nodemain:v1.0'
        HOST_PORT = 3000
        CONTAINER_PORT = 3000
    }

    stages {
        stage('Setup Configuration') {
            steps {

                    if (env.BRANCH_NAME == 'dev') {
                        echo "Configurando para el entorno DEV"
                        IMAGE_NAME = 'nodedev:v1.0' 
                        HOST_PORT = 3001
                    } else {
                        echo "Configurando para el entorno MAIN"
                    }
                    echo "Imagen a construir: ${IMAGE_NAME}"
                    echo "Puerto a exponer: ${HOST_PORT}"
                }
            }
        }

        stage('Build') {
            steps {
                echo "Instalando dependencias npm..."
                // Las instrucciones piden 'npm install'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Ejecutando pruebas..."
                sh 'npm test'
            }
        }

        stage('Docker build') {
            steps {
                echo "Construyendo imagen Docker: ${IMAGE_NAME}"
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                echo "Desplegando ${IMAGE_NAME} en el puerto ${HOST_PORT}"
                sh 'docker stop $(docker ps -a -q) || true'
                sh 'docker rm $(docker ps -a -q) || true'

                sh "docker run -d --expose ${CONTAINER_PORT} -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}"
            }
        }
    }
}