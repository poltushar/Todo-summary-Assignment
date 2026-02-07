

pipeline {
    agent any

   tools {
    maven 'maven-3'
    jdk 'jdk17'
  }

    environment {
        IMAGE_NAME = "todo-summary"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        IMAGE_NAME_FRONTEND = "todo-summart-frontend"  
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

     

        

        stage('Build Docker Image') {
            steps {
                sh """
                cd Backend/todo-summary-assistant
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                cd Frontend/todo
                docker build -t ${IMAGE_NAME_FRONTEND}:${IMAGE_TAG} .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerHubCred',
                    usernameVariable: 'dockerHubUser',
                    passwordVariable: 'dockerHubPass'
                )]) {
                    sh """
                    echo \$dockerHubPass | docker login -u \$dockerHubUser --password-stdin
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG}  $dockerHubUser/${IMAGE_NAME}:${IMAGE_TAG} 
                    docker push $dockerHubUser/${IMAGE_NAME}:${IMAGE_TAG}
                     docker tag ${IMAGE_NAME_FRONTEND}:${IMAGE_TAG}  $dockerHubUser/${IMAGE_NAME_FRONTEND}:${IMAGE_TAG}
                    docker push $dockerHubUser/${IMAGE_NAME_FRONTEND}:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        failure {
            echo 'CI pipeline failed'
        }
        success {
            echo 'CI pipeline completed successfully'
        }
    }
}

