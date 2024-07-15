pipeline {
agent any
environment {
   GIT_COMMIT_SHORT = sh(
     script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
     returnStdout: true
    )
}
/*tools {
   maven 'maven'
   jdk 'java'
}*/
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
    withSonarQubeEnv(credentialsId: 'colan-sonaqube-server-global-access-token', installationName: 'colan-sonarqube-server') {
         sh '''https://sonarqube.colanapps.in/bin/sonar-scanner \
         -Dsonar.projectKey=projectKey \
         -Dsonar.projectName=projectName \
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
        customHeaders: [[name: 'Authorization', value: "Bearer ${colan-sonaqube-server-global-access-token}"]],
        url: https://sonarqube.colanapps.in/
    )
                    def json = readJSON text: response.content
                    def status = json.projectStatus.status
                    
                    if (status == 'OK') {
                        echo "Quality gate passed: ${json.projectStatus.status}"
                    } else {
                        error "Quality gate failed: ${json.projectStatus.status}"
                    }
                }
            }
        }
}
}
