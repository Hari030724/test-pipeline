pipeline {
    agent any

    environment {
        qualityGateUrl = 'http://localhost:9000/api/qualitygates/project_status?projectKey=Pipelinetest&token=sqa_df14564ec2c340ce0c698466f20fd8f864e03638'
        SONARQUBE_API_TOKEN = 'sqa_df14564ec2c340ce0c698466f20fd8f864e03638' 
        SONARQUBE_SERVER_URL = 'http://sonarqube.colanapps.in' 
        SONARQUBE_PROJECT_KEY = 'Pipelinetest' 
    }

    stages {
        
     /*   stage('Build & Analysis') {
            steps {
                 environment {
      SCANNER_HOME = tool 'Sonar-scanner'
    }
            }
            }*/
        
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
         stage('SonarQube Analysis') {
            steps {
                script {
                 
                    withSonarQubeEnv(SONARQUBE) {
                        sh "${SONAR_SCANNER} -Dsonar.projectKey=Pipelinetest -Dsonar.sources=src -Dsonar.host.url=http://localhost:9000 -Dsonar.login=${env.SONARQUBE_API_TOKEN}"
                    }
                }
            }
        }
        
        stage('Check Quality Gate') {
            steps {
               script {
                 timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
                    
                   
                  
                }
            }
        }
          
                }

   
}
