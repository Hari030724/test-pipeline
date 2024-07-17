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
		timeout(time: 5, unit: 'MINUTES') {
		
				try {
					result = httpRequest "http://sonarqube.colanapps.in/api/project_analyses/search?project=sonar-quality-gate-maven-plugin:${env.BRANCH_NAME}"
					def analyses = readJSON text: result.content
					analysisId = analyses.analyses[0].key
					
					for (event in analyses.analyses[0].events) {
						if (event.category == "VERSION") {
							eventName = event.name
							break
						}
					}
					
					echo "AnalysisId: ${analysisId}"
					echo "EventName: ${eventName}"
					echo "Looking for: ${version}"
					
					if (eventName == "${version}") {
						return true
					} else {
						return false
					}
				} catch (NullPointerException e) {
					print "No analysis yet"
					return false
				}
			
		}		
     
		result = httpRequest "http://<sonarqube-instance>/api/qualitygates/project_status?analysisId=${analysisId}"
	
		def object = readJSON text: result.content
		def status = result.status

		echo "Quality Gate state: ${status}"

		if (object.projectStatus.status == "NONE") {
			error "No Quality Gate defined for ${env.BRANCH_NAME}"
		} else if (object.projectStatus.status != "OK") {
			error "Pipeline aborted due to quality gate failure: ${object.projectStatus.status}"
		} else {
			echo "Quality Gate passed (${object.projectStatus.status})"
		}
    }
    }
    
    post {
       always {
            echo "Pipeline finished with status: ${object.projectStatus.status}"
        }
    }

}
