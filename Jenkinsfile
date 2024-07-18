pipeline {
    agent any

    environment {
	    SONAR_HOME = tool 'colan-sonarqube-server'
        SONARQUBE_API_TOKEN = credentials('colan-sonaqube-server-global-access-token') 
        SONARQUBE_SERVER_URL = 'https://sonarqube.colanapps.in/dashboard?id=io.github.r0bb3n%3Asonar-quality-gate-maven-plugin' 
        SONARQUBE_PROJECT_KEY = 'io.github.r0bb3n:sonar-quality-gate-maven-plugin' 
    }

    stages {
        stage('Build & Analysis') {
            steps {
                withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
		sh ''' $SCANNER_HOME/bin/sonarqube-scanner -Dsonar.projectName=sonar-quality-gate-maven-plugin \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=io.github.r0bb3n:sonar-quality-gate-maven-plugin'''
              }
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
 stage("Quality Gate"){
	 steps {
		 script {
def qualityGateUrl = "${SONARQUBE_SERVER_URL}/api/qualitygates/project_status?projectKey=${SONARQUBE_PROJECT_KEY}"
def response = httpRequest(acceptType: 'APPLICATION_JSON',contentType: 'APPLICATION_JSON',
customHeaders: [[name: 'Authorization', value: "Bearer ${SONARQUBE_API_TOKEN}"]],url: "${qualityGateUrl}")
                    
def qualityGateStatus = readJSON text: response.content
def status = qualityGateStatus.projectStatus.status
                    
if (status == 'ERROR' || status == 'WARN') {
currentBuild.result = 'FAILURE'
error "SonarQube quality gate failed: ${status}"
    }
    }
	 }
 }
    }
    post {
       always {
            echo "Pipeline finished with status: ${status}"
        }
    }
    }
