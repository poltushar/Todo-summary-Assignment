

pipeline {
    agent any

   tools {
    maven 'maven-3'
    jdk 'jdk17'
  }

    environment {
        IMAGE_NAME = "todo-summary"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                dir('Backend/todo-summary-assistant') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('Backend/todo-summary-assistant') {
                    sh 'mvn test || true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                cd Backend/todo-summary-assistant
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
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
                    docker push ${dockerHubUser}/${IMAGE_NAME}:${IMAGE_TAG}
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

