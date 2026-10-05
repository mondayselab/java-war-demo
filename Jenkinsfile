pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'MAVEN_HOME'
    }
    environment {
        // CHANGE THIS TO YOUR DOCKER HUB USERNAME
        IMAGE_NAME = 'YOUR_DOCKERHUB_USERNAME/jenkins-java-webapp'
    }
    options {
        skipDefaultCheckout(true)
        timestamps()
    }
    stages {
        stage('Welcome') {
            steps {
                echo '========================================='
                echo ' Jenkins CI/CD Pipeline Started'
                echo '========================================='
            }
        }
        stage('Checkout') {
            steps {
                deleteDir()
                checkout scm
            }
        }
        stage('Maven Clean') {
            steps {
                bat 'mvn -B clean'
            }
        }
        stage('Maven Validate') {
            steps {
                bat 'mvn -B validate'
            }
        }
        stage('Maven Compile') {
            steps {
                bat 'mvn -B compile'
            }
        }
        stage('Maven Test') {
            steps {
                bat 'mvn -B test'
            }
        }
        stage('Maven Package') {
            steps {
                bat 'mvn -B package'
            }
        }
        stage('Maven Verify') {
            steps {
                bat 'mvn -B verify'
            }
        }
        stage('Maven Install') {
            steps {
                bat 'mvn -B install'
            }
        }
        stage('Check WAR') {
            steps {
                bat 'dir target'
            }
        }
        stage('Docker Build') {
            steps {
                bat '''
                    docker --version
                    docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .
                    docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest
                '''
            }
        }
        stage('Docker Push') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    bat '''
                        echo %DOCKER_TOKEN% | docker login -u %DOCKER_USER% --password-stdin

                        docker push %IMAGE_NAME%:%BUILD_NUMBER%
                        docker push %IMAGE_NAME%:latest

                        docker logout
                    '''
                }
            }
        }
        stage('Docker Run') {
            steps {
                bat '''
                    docker rm -f jenkins-java-webapp >nul 2>&1 || echo No old container found

                    docker run -d ^
                      --name jenkins-java-webapp ^
                      -p 9090:8080 ^
                      %IMAGE_NAME%:latest
                '''
            }
        }
        stage('Browser Test') {
            steps {
                bat '''
                    timeout /t 15 /nobreak >nul
                    curl -f http://localhost:9090/app/
                '''
            }
        }
    }
    post {
        success {
            echo '========================================='
            echo ' CI/CD PIPELINE SUCCESSFUL'
            echo ' Application: http://localhost:9090/app/'
            echo '========================================='
        }
        failure {
            echo '========================================='
            echo ' CI/CD PIPELINE FAILED'
            echo ' Check the Jenkins console output'
            echo '========================================='
        }
    }
}
