pipeline {
    agent any
    
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Tag of the Docker image')
    }
    
    stages {
         stage('Validation') {
            // En este stage validamos el "TAG" de la imagen que vamos a crear
            steps {  
              script {
               	def ImgTag = IMAGE_TAG

                // Aqui validamos que el "TAG" no tenga mas de 6 caracteres
                if(ImgTag.length() > 6) {
                    error("Asegúrese de que el TAG de la imagen que definió: $IMAGE_TAG es menor o igual a 6 caracteres!")
                }           

                def ImgType = /^[a-z0-9-.]+$/

                // Aqui validamos que el "TAG" solo pueda tener minúsculas, alfanuméricas, guiones y puntos.
                if(!IMAGE_TAG.matches(ImgType)) {
                    error("Sólo se permiten minúsculas, alfanuméricas, guiones y puntos.!")   
                }
                echo "El nombre de la imagen: $ImgTag es válido!"
               }
            }
        }

        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/israel870730/DevOps-Exercises']])
            }
        }
        
        stage('Build Docker Image in Subdirectory') {
            steps {
                script {
                    // Cambiar al directorio donde se encuentra el Dockerfile
                    dir('6-Jenkins/myapp/') {
                        // Construir la imagen Docker desde el Dockerfile en el directorio especificado
                        dockerImage = docker.build "870730/myapp"
                    }
                }
            }
        }
        
        stage('Docker Tag'){
            steps {
                script {
                    dockerImage.tag("${params.IMAGE_TAG}")
                }   
            }   
        }
        
        stage('Uploading Img'){
            steps {
                script{
                    docker.withRegistry('', 'dockerhub_id') {
                       dockerImage.push("${params.IMAGE_TAG}")
                    }
                }
            }
        }
    }
}
