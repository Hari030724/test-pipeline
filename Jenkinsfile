pipeline {
    agent any

    environment {
        qualityGateUrl = 'http://localhost:9000/api/qualitygates/project_status?projectKey=Pipelinetest&token=sqa_df14564ec2c340ce0c698466f20fd8f864e03638'
        SONARQUBE_API_TOKEN = 'sqa_df14564ec2c340ce0c698466f20fd8f864e03638' 
        SONARQUBE_SERVER_URL = 'http://sonarqube.colanapps.in' 
        SONARQUBE_PROJECT_KEY = 'Pipelinetest' 
    }

    stages {
        
        stage('Build & Analysis') {
            steps {
                 environment {
      SCANNER_HOME = tool 'Sonar-scanner'
    }
            }
            }
        
        stage('Test') {
            steps { 
                sh "mvn test"
            }
        }

         stage('Cleanup Workspace') {
    steps {
        deleteDir() 
    }
}
        
        stage('Check Quality Gate') {
            steps {
               script {
                    def response = sh(script: "curl -u ${SONARQUBE_API_TOKEN}: ${qualityGateUrl}", returnStdout: true).trim()
                    
                    def qualityGateStatus = readJSON text: response
                    def status = qualityGateStatus.projectStatus.status
                    
                    if (status != 'OK') {
            
                        error "SonarQube quality gate status: ${status}"
                        currentBuild.result = 'FAILURE'
                        
                    } 
                   else { 
                       echo "SonarQube quality gate status: ${status}"
                      currentBuild.result = 'SUCCESS'
               }
                }
            }
        }
          stage('Pipeline Status') {
                steps {
                
            echo "Pipeline finished with status: ${currentBuild.result}"
    
}
    }
                }

   
}
