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
        
      stage('Check Quality Gate') {
            steps {
                script {
                  def qualityGateUrl = "${SONARQUBE_SERVER_URL}/api/qualitygates/project_status?projectKey=${SONARQUBE_PROJECT_KEY}"
			def response = httpRequest(acceptType: 'APPLICATION_JSON',contentType: 'APPLICATION_JSON',
                         customHeaders: [[name: 'Authorization', value: "Bearer ${SONARQUBE_API_TOKEN}"]],url: "${qualityGateUrl}")
			def qualityGateStatus = readJSON text: response.content
                        def status = qualityGateStatus.projectStatus.status
                    if (status == 'ERROR' || status == 'WARN') {
			currentBuild.result = 'FAILURE'
                        env.project_status = 'Failed'
		        currentBuild.abort('Aborting operation as Quality Gate has failed!')
                    } else {
                        currentBuild.result = 'SUCCESS'
                        env.project_status = 'Passed'
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
