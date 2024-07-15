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
            timeout(time: 1, unit: 'MINUTES') {
               while (taskStatus in ['PENDING', 'IN_PROGRESS']) {
    sleep 30  // Adjust sleep duration as needed
    taskStatus = checkSonarQubeTaskStatus('84c86771-8e67-450b-b841-48f9c31b1c9a')
              }
            }
          }
        
        }
 
      }
}
