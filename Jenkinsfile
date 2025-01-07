pipepipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'flask-app'
        DOCKER_TAG = 'latest'
        DOCKER_REPO = 'tu-repositorio' // Cambia esto por tu repositorio
        CONTAINER_NAME = 'flask-test-container'
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Construir la imagen
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        
        stage('Run Container') {
            steps {
                script {
                    // Ejecutar el contenedor
                    sh "docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    // Esperar a que la aplicación esté lista
                    sh 'sleep 10'
                }
            }
        }
        
        stage('Test API') {
            steps {
                script {
                    // Test usando netcat
                    sh '''
                        response=$(echo -e "GET / HTTP/1.1\r\nHost: localhost:5000\r\n\r\n" | nc localhost 5000)
                        if echo "$response" | grep -q "Hello, World!"; then
                            echo "Test passed!"
                        else
                            echo "Test failed!"
                            exit 1
                        fi
                    '''
                }
            }
        }
        
      #  stage('Push to Repository') {
      #      steps {
      #          script {
      #              // Etiquetar y subir la imagen al repositorio
      #              sh """
      #                  docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REPO}/${DOCKER_IMAGE}:${DOCKER_TAG}
      #                  docker push ${DOCKER_REPO}/${DOCKER_IMAGE}:${DOCKER_TAG}
      #              """
      #          }
      #      }
     #   } # Fin Stage
    }
    
    post {
        always {
            script {
                // Limpiar: detener y eliminar el contenedor
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                """
            }
        }
    }
}
