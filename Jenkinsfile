pipeline {
    agent any
    tools {
        maven 'maven'
    }

    environment {
        DOCKER_IMAGE = 'jegadeesh86/springboot'
    }

    stages {
        stage('Pre-Check Docker') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/jegadeeshmanit/springBoot.git'
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
        docker stop Spring_boot_app || true
        docker rm Spring_boot_app || true
        docker pull jegadeesh86/springboot
        docker run -d --name Spring_boot_app -p 8081:8081 jegadeesh86/springboot
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
