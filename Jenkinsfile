pipeline {
    agent any

    environment {
        qualityGateUrl = 'https://sonarqube.colanapps.in/api/qualitygates/project_status?projectKey=io.github.r0bb3n%3Asonar-quality-gate-maven-plugin'
        //SONARQUBE_API_TOKEN = 'sqa_2de1ed443d10f46e6507693733fc93a39648a212' 
        SONARQUBE_SERVER_URL = 'http://sonarqube.colanapps.in' 
        SONARQUBE_PROJECT_KEY = 'io.github.r0bb3n:sonar-quality-gate-maven-plugin' 
    }

    stages {
        
        stage('Build & Analysis') {
            steps {
                withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
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
                    def response = sh(script: "curl -u :${qualityGateUrl}", returnStdout: true).trim()
                    
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
