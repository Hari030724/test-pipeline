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
              def qualityGateUrl = "https://sonarqube.colanapps.in/api/qualitygates/project_status"
                def response = httpRequest(
                        acceptType: 'APPLICATION_JSON',
                        contentType: 'APPLICATION_JSON',
                        customHeaders: [[name: 'Authorization', value: "Bearer ${colan-sonaqube-server-global-access-token}"]],
                        url: "${qualityGateUrl}?projectKey=${colan-sonarqube-server}")
        
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
   post {
        success {
            echo 'Pipeline successful!'
        }
        failure {
            echo 'Pipeline failed!'
        }
      }
      }

