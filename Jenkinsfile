pipeline {
    agent any

    environment {
        DOCKER_USER = 'haroldbg'
        DOCKER_PASS = credentials('dockerhub-pass') // 👈 ID de tus credenciales en Jenkins
    }

    stages {
        stage('Clonar Repositorios') {
            steps {
                echo '📦 Clonando los repositorios del Backend y Frontend...'
                sh '''
                    rm -rf task-movil-api task-movil-app || true
                    git clone https://github.com/camilo28k/task-movil-api.git
                    git clone https://github.com/camilo28k/task-movil-app.git
                '''
            }
        }

        stage('Construir Backend') {
            steps {
                dir('task-movil-api') {
                    echo '⚙️ Construyendo imagen del Backend...'
                    sh '''
                        mvn clean package -DskipTests
                        docker build -t haroldbg/task-movil-api:latest .
                    '''
                }
            }
        }

        stage('Construir Frontend') {
            steps {
                dir('task-movil-app/crudApp') { // 👈 el Dockerfile está dentro de crudApp
                    echo '⚙️ Construyendo imagen del Frontend...'
                    sh '''
                        npm install
                        npm run build
                        docker build -t haroldbg/task-movil-frontend:latest .
                    '''
                }
            }
        }

        stage('Subir imágenes a Docker Hub') {
            steps {
                echo '⬆️ Subiendo imágenes a Docker Hub...'
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push haroldbg/task-movil-api:latest
                        docker push haroldbg/task-movil-frontend:latest
                    '''
                }
            }
        }

        stage('Desplegar con Docker Compose') {
            steps {
                echo '🚀 Desplegando toda la aplicación (PostgreSQL + Backend + Frontend)...'
                sh '''
                    docker compose down || true
                    docker compose up -d --build
                '''
            }
        }
    }

    post {
        success {
            echo '✅ ¡Despliegue completo con éxito! La app está en funcionamiento.'
        }
        failure {
            echo '❌ Error en el pipeline. Revisa los logs en Jenkins.'
        }
    }
}
