pipeline {
    agent any

    environment {
        qualityGateUrl = 'https://sonarqube.colanapps.in/web_api/api/qualitygates/project_status?projectKey=io.github.r0bb3n%3Asonar-quality-gate-maven-plugin&metric=alert_status&token=sqa_2de1ed443d10f46e6507693733fc93a39648a212'
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

                    //echo "Quality Gate Status Response: ${response.content}"

                    def qualityGateStatus = readJSON text: response.content

                    def status = qualityGateStatus.projectStatus.status
                    if (status != 'OK') {
                        echo 'Quality Gate status Failed'
            
                    } else {
                        echo 'Quality Gate status Passed'
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
