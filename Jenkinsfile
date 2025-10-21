pipeline {
    agent any

    environment {
        PATH = '/usr/local/bin:${env.PATH}'
    }

    stages {
        // Etapa para detener los contenedores de docker
        stage('Parando los servicios') {
            steps {
                sh '''
                    docker compose -p demo-d down || true
                '''
            }
        }

        // Etapa para eliminar las imágenes viejas
        stage('Eliminando las imágenes anteriores') {
            steps {
                sh '''
                    IMAGES=$(docker images --filter "label=com.docker.compose.project=demo-d" -q)
                    if [ -n "$IMAGES" ]; then
                        docker rmi -f $IMAGES
                    else
                        echo 'No hay imágenes por borrar...'
                    fi
                '''
            }
        }

        // Etapa para bajar la actualización del repo
        stage('Bajando la actualización del repo') {
            steps {
                checkout scm
            }
        }

        // Etapa para construir y desplegar
        stage('Construyendo y desplegando') {
            steps {
                sh '''
                    docker compose up --build -d
                '''
            }
        }
    }

    // Post actions
    post {
        success {
            echo 'Pipeline completado exitosamente'
        }

        failure {
            echo 'Ocurrió un error durante la ejecución del pipeline'
        }

        always {
            echo 'Ejecución del pipeline finalizada'
        }
    }
}