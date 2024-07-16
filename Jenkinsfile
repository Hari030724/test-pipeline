pipeline {
        agent none
        stages {
          stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
          stage("Quality Gate") {
            steps {
            timeout(time: 10, unit: 'MINUTES') {
               def qg = waitForQualityGate()
                if (qg.status != 'OK'){
                       waitForQualityGate abortPipeline: true
                            
            }
          }
     
        }
        }
      }
}
