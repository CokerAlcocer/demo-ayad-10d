pipeline {
    agent any

    stages {
        // Paramos todos los servicios creados con el docker-compose.yml
        stage('Stopping services') {
            steps {
                sh '''
                    docker compose -p demo down || true
                '''
            } 
        }

        // Eliminamos todas las imagenes creadas con el docker-compose.yml
        stage('Deleting old images') {
            steps {
                sh '''
                    IMAGES=$(docker images --filter "label=com.docker.compose.project=demo" -q)
                    if [ -n "$IMAGES" ]; then
                        docker rmi -f $IMAGES
                    else
                        echo "No hay imágenes para borrar"
                    fi
                '''
            } 
        }

        // A raiz del job creado en Jenkins, le decimos que de la configuración de git jale cambios
        stage('Pulling update') {
            steps {
                checkout scm
            }
        }

        // Construimos nuevamente todos los servicios
        stage('Build and Deploy') {
            steps {
                sh '''
                    docker compose up --build -d
                '''
            } 
        }
    }

    post {
        success {
            echo '✅ Pipeline completado con éxito'
        }

        failure {
            echo '❌ Hubo un error en el pipeline'
        }

        always {
            echo '🔄 Pipeline terminado'
        }
    }
}