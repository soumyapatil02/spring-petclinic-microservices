pipeline {
    agent any
    
    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }
    
    stages {
        
        stage('Checkout') {
            steps {
                echo 'Cloning the repository...'
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/soumyapatil02/spring-petclinic-microservices.git'
            }
        }
        
        stage('Maven Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
        }
        
        stage('Docker Build') {
            steps {
                echo 'Building Docker images...'
                sh '''
                    docker build -t $DOCKERHUB_CREDENTIALS_USR/petclinic-customers:$BUILD_NUMBER \
                        spring-petclinic-customers-service/
                    docker build -t $DOCKERHUB_CREDENTIALS_USR/petclinic-vets:$BUILD_NUMBER \
                        spring-petclinic-vets-service/
                    docker build -t $DOCKERHUB_CREDENTIALS_USR/petclinic-visits:$BUILD_NUMBER \
                        spring-petclinic-visits-service/
                '''
            }
        }
        
        stage('Docker Push') {
            steps {
                echo 'Pushing images to DockerHub...'
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $DOCKERHUB_CREDENTIALS_USR/petclinic-customers:$BUILD_NUMBER
                    docker push $DOCKERHUB_CREDENTIALS_USR/petclinic-vets:$BUILD_NUMBER
                    docker push $DOCKERHUB_CREDENTIALS_USR/petclinic-visits:$BUILD_NUMBER
                '''
            }
        }
        
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check the logs.'
        }
    }
}