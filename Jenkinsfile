pipeline {
    agent any
    tools {
        maven 'MAVEN-HOME'
    }
    stages {
        stage('git repo & clean') {
            steps {
                bat "git clone https://github.com/mondayselab/java-war-demo.git"
                bat "mvn clean -f java-war-demo"
            }
        }
        stage('maven lifecycle') {
            steps {
                bat "mvn install -f java-war-demo"
                bat "mvn test -f java-war-demo"
                bat "mvn package -f java-war-demo"
            }
        }
        stage('docker build') {
            steps {
                bat "docker build -t mondayselab/jenkins-java-webapp java-war-demo"
            }
        }
        stage('docker push') {
            steps {
                bat "docker push mondayselab/jenkins-java-webapp"
            }
        }
        stage('docker run') {
            steps {
                bat "docker rm -f jenkins-java-webapp"
                bat "docker run -d --name jenkins-java-webapp -p 9090:8080 mondayselab/jenkins-java-webapp"
            }
        }
    }
}
