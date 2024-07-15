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
             waitForQualityGate   
                    {echo 'Build succeeded!'}
              }
            }
          }
      stage('Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }        
        }
 
      }

