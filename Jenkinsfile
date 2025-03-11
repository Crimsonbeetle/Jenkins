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
        stage('Test') {
            agent { docker { image 'node:22.14.0-alpine3.21' } }
            steps {
                sh 'node --eval "console.log(process.arch,process.platform)"'
            }
        }
        stage('Deploy') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    retry(3) {
                        sh './flakey-deploy.sh'
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
        }
    }
}