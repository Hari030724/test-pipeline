pipeline {
    agent any

    environment {
        //MAVEN_HOME = tool name: 'Maven', type: 'maven'
        //SONARQUBE_SCANNER_HOME = tool name: 'colan-sonarqube-server', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        SONARQUBE_API_TOKEN = credentials('colan-sonaqube-server-global-access-token') 
        SONARQUBE_SERVER_URL = 'http://sonarqube.colanapps.in' 
        SONARQUBE_PROJECT_KEY = 'sonar-quality-gate-maven-plugin' 
    }

    stages {
        
        stage('Build') {
            steps {
                withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
        }
        
        /*stage('Test') {
            steps {
                sh "Maven/bin/mvn test"
            }
        }*/
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('colan-sonarqube-server') {
                    sh "colan-sonarqube-server/bin/sonar-scanner"
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
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
