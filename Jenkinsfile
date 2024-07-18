pipeline {
    agent any
    environment {
        SONARQUBE_API_TOKEN = credentials('colan-sonaqube-server-global-access-token') 
        SONARQUBE_SERVER_URL = 'https://sonarqube.colanapps.in/dashboard?id=io.github.r0bb3n%3Asonar-quality-gate-maven-plugin' 
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
        
 stage("Quality Gate"){
	 steps {
		script {
            timeout(time: 5, unit: 'MINUTES') {
       waitForQualityGate abortPipeline: true
       }
                  
	 }
 }
    }
    }
    post {
       always {
            echo "Pipeline finished with status: ${currentBuild.result}"
        }
    }
    }
