pipeline {
    agent any
    triggers {
        pollSCM('H/2 * * * *')
    }
    tools {
        jdk 'JDK21'
        maven 'M3'
    }
    environment {
        IMAGE = 'youssefhaggui_4fintech1_gestionprojets'
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Tests') {
            steps { dir('backend') { sh 'mvn test' } }
        }
        stage('Package') {
            steps { dir('backend') { sh 'mvn package -DskipTests' } }
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
        stage('Docker build') {
            steps { dir('backend') { sh 'docker build -t $IMAGE .' } }
        }
        stage('Docker push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
                    sh 'docker tag $IMAGE $DH_USER/$IMAGE:latest'
                    sh 'docker push $DH_USER/$IMAGE:latest'
                }
            }
        }
    }
    post {
        always { junit allowEmptyResults: true, testResults: 'backend/target/surefire-reports/*.xml' }
    }
}
