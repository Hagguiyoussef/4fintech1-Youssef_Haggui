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
                        sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=gestion-projets'
                    }
                }
            }
        }
        stage('Archive') {
            steps { archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true }
        }
    }
}
