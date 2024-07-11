pipeline {
    agent any
    environment {
        mvnHome = tool name: 'Maven', type: 'maven'
        // Replace with your Nexus repository URL
       // nexusUrl = 'https://your-nexus-repo-url'
        // Replace with your SonarQube server URL
        sonarServerUrl = 'https://sonarqube.colanapps.in/projects'
        // Replace with your SonarQube project key and name
        sonarProjectKey = 'test-pipeline'
        sonarProjectName = 'test-pipeline'
    }
    stages {
  stage('Checkout') {
    checkout scm
  }
        stage('Build') {
            steps {
                sh "${mvnHome}/bin/mvn clean install"
            }
        }
  stage('SonarQube Analysis') {
    withSonarQubeEnv('sonar_server') {
      sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=test-pipeline -Dsonar.projectName='test-pipeline'"
    }
  }
}
     post {
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed :('
        }
    }
}
