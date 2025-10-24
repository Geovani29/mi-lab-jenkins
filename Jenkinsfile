pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        IMAGE_NAME = 'nodemain:v1.0'
        HOST_PORT = 3000
        CONTAINER_PORT = 3000
        CONTAINER_NAME = 'app-main'
    }

    stages {
        stage('Setup Configuration') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'dev') {
                        echo "Configurando para el entorno DEV"
                        IMAGE_NAME = 'nodedev:v1.0'
                        HOST_PORT = 3001
                        CONTAINER_NAME = 'app-dev'
                    } else {
                        echo "Configurando para el entorno MAIN"
                    }
                    echo "Imagen a construir: ${IMAGE_NAME}"
                    echo "Puerto a exponer: ${HOST_PORT}"
                    echo "Nombre del contenedor: ${CONTAINER_NAME}"
                }
            }
        }

        stage('Build') {
            steps {
                echo "Instalando dependencias npm..."
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
                echo "Desplegando ${IMAGE_NAME} como ${CONTAINER_NAME} en el puerto ${HOST_PORT}"

                sh "docker stop ${CONTAINER_NAME} || true"

                sh "docker rm ${CONTAINER_NAME} || true"

                sh "docker run -d --name ${CONTAINER_NAME} --expose ${CONTAINER_PORT} -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}"
            }
        }
    }
}
