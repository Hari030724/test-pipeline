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
 
def response = sh(script: "curl -u ${SONARQUBE_API_TOKEN}: ${qualityGateUrl}", returnStdout: true).trim()

                    echo "Quality Gate Status Response: ${response.content}"

                    def json = readJSON text: response.content

                    def qualityGateStatus = json?.projectStatus?.status
                    if (qualityGateStatus != 'OK') {
                        error "Quality Gate status is not OK: ${qualityGateStatus}"
                    
                    } else {
                        echo "Quality Gate status is OK"
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
