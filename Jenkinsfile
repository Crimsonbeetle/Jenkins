pipeline {
    agent any
    stages {
        stage('Build with Maven') {
            agent { docker { image 'maven:3.9.9-eclipse-temurin-21-alpine' } }
            steps {
                sh 'mvn --version'
            }
        }
        stage('Build on Windows') {
            steps {
                bat 'echo Hello World'
                bat 'dir'
            }
        }
    }
}