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
                  def qg = sh "curl -X GET {'https://sonarqube.colanapps.in/api/qualitygates/project_status?projectKey=io.github.r0bb3n:sonar-quality-gate-maven-plugin'}"
                    if (qg.status == 'OK') {
                        currentBuild.result = 'SUCCESS'
                        env.project_status = 'Passed'
                    } else {
                        currentBuild.result = 'FAILURE'
                        env.project_status = 'Failed'
		        currentBuild.abort('Aborting pipeline as Quality Gate has failed!')
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
