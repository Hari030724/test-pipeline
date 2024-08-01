pipeline {
    agent any

    environment {
        qualityGateUrl = 'https://sonarqube.colanapps.in/api/qualitygates/project_status?projectKey=io.github.r0bb3n%3Asonar-quality-gate-maven-plugin&metric=alert_status&token=sqb_40ab086d5cc57fc4a436f7eed56453afb106841c'
        SONARQUBE_API_TOKEN = 'sqa_2de1ed443d10f46e6507693733fc93a39648a212' 
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
 
def response = httpRequest(acceptType: 'APPLICATION_JSON',contentType: 'APPLICATION_JSON',
customHeaders: [[name: 'Authorization', value: "Bearer ${SONARQUBE_API_TOKEN}"]], url: "${qualityGateUrl}")

def qualityGateStatus = readJSON text: response.content
def status = qualityGateStatus.projectStatus.status
                    
if (status == 'ERROR' || status == 'WARN') {
currentBuild.result = 'FAILURE'
error "SonarQube quality gate failed: ${qualityGateStatus.projectStatus.status}"
    exit 1
}
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
