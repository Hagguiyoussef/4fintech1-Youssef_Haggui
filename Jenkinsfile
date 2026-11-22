pipeline {
    agent any
    tools {
        jdk 'JDK21'
        maven 'M3'
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            steps { dir('backend') { sh 'mvn clean package' } }
        }
        stage('Sonar') {
            steps {
                dir('backend') {
                    withSonarQubeEnv('sonar') {
                        sh 'mvn sonar:sonar -Dsonar.projectKey=gestion-projets'
                    }
                }
            }
        }
        stage('Archive') {
            steps { archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true }
        }
    }
}
