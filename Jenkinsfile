pipeline {
    agent any
    tools {
        maven 'maven'
    }

    environment {
        DOCKER_IMAGE = 'benjeo/spring-boot-app'
    }

    stages {
        stage('Pre-Check Docker') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/jegadeeshmanit/springBoot.git'
            }
        }

        stage('Build JAR') {
    steps {
        sh 'chmod +x ./mvnw' // Add this line to grant execute permissions
        sh './mvnw clean package'
        archiveArtifacts artifacts: 'target/spring_app_sak-0.0.1-SNAPSHOT.jar', fingerprint: true
    }
}



        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $DOCKER_IMAGE
                    """
                }
            }
        }

        stage('Deploy Application') {
    steps {
        sh """
        docker stop spring-boot-app || true
        docker rm spring-boot-app || true
        docker pull benjeo/spring-boot-app
        docker run -d --name spring-boot-app -p 8081:8081 /spring-boot-app
        """
    }
    }

    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline execution failed!'
        }
    }
}
