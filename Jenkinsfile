pipeline {
    agent any

    stages {
        stage('Clonar Repositorios') {
            steps {
                sh 'git clone https://github.com/camilo28k/task-movil-api.git'
                sh 'git clone https://github.com/camilo28k/task-movil-app.git'
            }
        }

        stage('Construir Backend') {
            steps {
                dir('task-movil-api') {
                    sh 'mvn clean package -DskipTests'
                    sh 'docker build -t camilo28k/task-movil-api .'
                }
            }
        }

        stage('Construir Frontend') {
            steps {
                dir('task-movil-app') {
                    sh 'npm install'
                    sh 'npm run build'
                    sh 'docker build -t camilo28k/task-movil-app .'
                }
            }
        }

        stage('Desplegar con Docker Compose') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }
}
