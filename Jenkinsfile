pipeline {
    agent any

    environment {
        MAVEN_HOME = tool name: 'Maven', type: 'maven 3.9.8'
        SONARQUBE_SCANNER_HOME = tool name: 'colan-sonarqube-server', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        SONARQUBE_TOKEN = credentials('colan-sonaqube-server-global-access-token') 
        SONARQUBE_SERVER_URL = 'http://sonarqube.colanapps.in/' 
        SONARQUBE_PROJECT_KEY = 'colan-sonaqube-server-global-access-token' 
    }
    stages {
           
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
        
        stage('Build & SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SONARQUBE_SCANNER_HOME') {
                  //  sh 'mvn clean package sonar:sonar'
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                }
            }
        }
        
        stage('Check Quality Gate') {
            steps {
                script {
                    def qualityGateUrl = "${SONARQUBE_SERVER_URL} api/qualitygates/project_status"
                    
                    def response = httpRequest(
                        acceptType: 'APPLICATION_JSON',
                        contentType: 'APPLICATION_JSON',
                        customHeaders: [[name: 'Authorization', value: "Bearer ${SONARQUBE_TOKEN}"]],
                        url: "${qualityGateUrl}?projectKey=${SONARQUBE_PROJECT_KEY}")
        
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
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
