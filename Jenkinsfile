pipeline {
agent any
environment {
   GIT_COMMIT_SHORT = sh(script: "printf \$(git rev-parse --short ${GIT_COMMIT})",
     returnStdout: true
    )
}

stages {
  stage('Build project') {
    steps {
     withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
              }
    }
  }
  stage('SonarQube analysis') {
    steps {
    withSonarQubeEnv(credentialsId: 'colan-sonaqube-server-global-access-token', installationName: 'colan-sonaqube-server') {
         sh '''/sonar/sonar-scanner/bin \
         -Dsonar.projectKey=io.github.r0bb3n:sonar-quality-gate-maven-plugin \
         -Dsonar.projectName=sonar-quality-gate-maven-plugin \
         -Dsonar.sources=src/ \
         -Dsonar.java.binaries=target/classes/ \
         -Dsonar.exclusions=src/test/java/****/*.java \
         -Dsonar.java.libraries=/var/lib/jenkins/.m2/**/*.jar \
         -Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
       }
     }
}
   stage('Quality Gate') {
     steps {
       timeout(time: 5, unit: 'MINUTES') {
       waitForQualityGate abortPipeline: true
       }
  }
}
}
}
