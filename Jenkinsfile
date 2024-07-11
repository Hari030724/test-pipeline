pipeline {
    agent any
    stages {
  stage('SCM') {
    checkout scm
  }
  stage('SonarQube Analysis') {
      agent any
    def mvn = tool 'Maven';
    withSonarQubeEnv('sonar_server') {
      sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=test-pipeline -Dsonar.projectName='test-pipeline'"
    }
  }
}
}
