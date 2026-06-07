pipeline {
    agent any

    tools {
        jdk 'JDK-23'
        maven 'Maven-3.9'
    }

    triggers { 
        pollSCM('H/2 * * * *')
    }

    environment {
        DOCKER_IMAGE = "vermas2012d/etp_jenkins_java"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "etp_jenkins_java"
        PORT = "8081"
    }

    stages {
 
        stage('clone repo'){
            steps{
                git url: "https://github.com/Vermas2004d/Practice2.git",
                    branch: 'main'
            }
        }

        stage('Build Application'){
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image'){
            steps {
            sh """
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }

        stage('Login DockerHub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

          stage('Push Image') {
            steps {
                sh """
                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }

        stage('Stop Old Container') {
            steps {
                sh """
                    docker rm -f ${CONTAINER_NAME} || true
                """
            }
        }

         stage('Run New Container') {
            steps {
                sh """
                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p ${PORT}:8080 \
                    ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }
    }

     post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}


