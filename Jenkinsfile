pipeline {
    agent any

    environment {
        MAVEN_HOME = tool name: 'Maven', type: 'maven'
        SONARQUBE_SCANNER_HOME = tool name: 'SonarQubeScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        SONARQUBE_API_TOKEN = credentials('sqa_2de1ed443d10f46e6507693733fc93a39648a212') 
        SONARQUBE_SERVER_URL = 'http://sonarqube.colanapps.in' 
        SONARQUBE_PROJECT_KEY = 'Test-pipeline-Sonar' 
    }
    stages {
      
        stage('Checkout') {
           steps {
                git branch:'master',credentialsId: 'gurunathantest',url: 'https://github.com/gurunathantest/sonar-quality-gate-maven-plugin.git'
            } 
        }
        stage('Build') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean install"
            }  
        }
        
        stage('Test') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn test"
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sqa_2de1ed443d10f46e6507693733fc93a39648a212', installationName: 'test-pipeline') {
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                }
            }
        }
        
        stage('Check Quality Gate') {
            steps {
                script {
                    def qualityGateUrl = "${SONARQUBE_SERVER_URL}/api/qualitygates/project_status"
                    def response = httpRequest(
                        acceptType: 'APPLICATION_JSON',
                        contentType: 'APPLICATION_JSON',
                        customHeaders: [[name: 'Authorization', value: "Bearer ${SONARQUBE_API_TOKEN}"]],
                        url: "${qualityGateUrl}?projectKey=${SONARQUBE_PROJECT_KEY}"
                    )
                    
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
    }

    post {
        success {
            echo 'Pipeline successful!'
            // Additional actions upon success
        }
        failure {
            echo 'Pipeline failed!'
            // Additional actions upon failure
        }
    }
}
