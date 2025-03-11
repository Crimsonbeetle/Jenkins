pipeline {
    agent any

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }

    options {
        skipStagesAfterUnstable()
    }

    stages {
        stage('Build with Maven') {
            agent { docker { image 'maven:3.9.9-eclipse-temurin-21-alpine' } }
            steps {
                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"
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
                sh './gradlew check'
            }
        }
        stage('Deploy - Staging') {
            steps {
                sh './deploy staging'
                sh './run-smoke-tests'
            }
        }
        stage('Sanity check') {
            steps {
                input "Does the staging environment look ok?"
            }
        }
        stage('Deploy - Production') {
            steps {
                sh './deploy production'
            }
        }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() // Clean up workspace
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
            junit 'build/reports/**/*.xml'
        }
        success {
            echo 'I succeeded!'
            slackSend channel: '#ops-room',
                      color: 'good',
                      message: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
            mail to: 'team@example.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
        changed {
            echo 'Things were different before...'
        }
    }
}