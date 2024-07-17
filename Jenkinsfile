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
		timeout(time: 5, unit: 'MINUTES') {
		
		def result = httpRequest "http://sonarqube.colanapps.in/api/qualitygates/project_status?projectKey=${SONARQUBE_PROJECT_KEY}"
	
		def object = readJSON text: result.content

		echo "Quality Gate state: ${status}"

		if (object.projectStatus.status == "NONE") {
			error "No Quality Gate defined"
		} else if (object.projectStatus.status != "OK") {
			error "Pipeline aborted due to quality gate failure: ${object.projectStatus.status}"
		} else {
			echo "Quality Gate passed (${object.projectStatus.status})"
		}
    }
    }
 }
    post {
       always {
            echo "Pipeline finished with status: ${object.projectStatus.status}"
        }
    }
    }
}
