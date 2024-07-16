pipeline {
    agent any

    environment {
        SONARQUBE_API_TOKEN = credentials('colan-sonaqube-server-global-access-token') 
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
        
      stage('Check Quality Gate') {
            steps {
                script {
                    def result = httpRequest "http://sonarqube.colanapps.in/api/qualitygates/project_status?projectKey=${SONARQUBE_PROJECT_KEY}"
	         sh "curl -X GET ${result}"
		def object = readJSON text: result.content
		def status = result.status
		echo "Quality Gate state: ${status}"

		if (object.projectStatus.status == "NONE") {
			error 'No Quality Gate defined'
		} else if (object.projectStatus.status != "OK") {
			error "Pipeline aborted due to quality gate failure: ${object.projectStatus.status}"
		} else {
			echo "Quality Gate passed (${object.projectStatus.status})"
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
