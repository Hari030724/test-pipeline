pipeline {
agent any
environment {
   GIT_COMMIT_SHORT = sh(
     script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
     returnStdout: true
    )
}
   
stages {
  stage('Build project') {
    steps {
      sh 'mvn clean package sonar:sonar'
    }
  }
  stage('SonarQube analysis') {
    environment {
      SCANNER_HOME = tool 'Sonar-scanner'
    }
    steps {
    withSonarQubeEnv(credentialsId: 'Test-pipeline-Sonar', installationName: 'test-pipeline') {
         sh '''https://sonarqube.colanapps.in/bin/sonar-scanner \
         -Dsonar.projectKey=test-pipeline \
         -Dsonar.projectName=test-pipeline \
         -Dsonar.sources=src/ \
         -Dsonar.java.binaries=target/classes/ \
         -Dsonar.exclusions=src/test/java/****/*.java \
         -Dsonar.java.libraries=/var/lib/jenkins/.m2/**/*.jar \
         -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
       }
     }
}
        stage('Check Quality Gate') {
            steps {
                script {
                    def qualityGateUrl = "https://sonarqube.colanapps.in/api/qualitygates/project_status"
                    def response = httpRequest(
        acceptType: 'APPLICATION_JSON',
        contentType: 'APPLICATION_JSON',
        customHeaders: [[name: 'Authorization', value: "Bearer ${Test-pipeline-Sonar}"]],
        url: "https://sonarqube.colanapps.in/")
                    def qc = readJSON text: response.content
                    def stat = qc.projectStatus.status
                    
                    if (stat == 'OK') {
                        echo "Quality gate passed: ${stat}"
                    } else {
                        error "Quality gate failed: ${stat}"
                    }
                }
            }
        }
}
}
