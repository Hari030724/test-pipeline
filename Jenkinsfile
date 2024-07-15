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
                 steps {
                withSonarQubeEnv('colan-sonarqube-server') {
                    sh "https://sonarqube.colanapps.in/bin/mvn sonar:sonar -Dsonar.projectKey=${test-pipeline} -Dsonar.projectName='${test-pipeline}' -Dsonar.host.url=${https://sonarqube.colanapps.in}"
                }
              }
            }
          }
     
        }
        }
      }

