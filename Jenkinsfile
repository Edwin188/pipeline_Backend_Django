pipeline {
    agent any

    environment {
        // Reemplace con su usuario real de Docker Hub
        DOCKER_IMAGE = "jegr188/backend-django"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('1. Descarga de Código (Checkout)') {
            steps {
                echo 'Descargando código desde el repositorio de GitHub...'
                checkout scm
            }
        }

        stage('2. Pruebas Automatizadas (Testing)') {
            steps {
                echo 'Ejecutando la suite de pruebas del Backend en Django...'
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                    python manage.py test
                '''
            }
        }

        stage('3. Construcción de Imagen (Build Docker Image)') {
            steps {
                echo 'Construyendo la imagen de contenedor Docker...'
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    dockerImage.tag("latest")
                }
            }
        }

        stage('4. Publicación en Registro (Push to Docker Hub)') {
            steps {
                echo 'Publicando imagen en Docker Hub...'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        dockerImage.push("${DOCKER_TAG}")
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }

    post {
        success {
            echo '¡El Pipeline de CI/CD se ejecutó con ÉXITO! La nueva versión está publicada.'
        }
        failure {
            echo '¡ERROR en el Pipeline! La compilación o las pruebas fallaron. Revisa los logs.'
        }
    }
}