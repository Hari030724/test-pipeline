pipeline {
    agent any

    environment {
        // Define Maven and SonarQube related variables
        mvnHome = tool name: 'Maven', type: 'maven'
        sonarProjectKey = 'Test-pipeline'
        sonarServerUrl = 'https://sonarqube.colanapps.in/'
    }

    stages {
        stage('Build and SonarQube Analysis') {
            steps {
                script {
                    def jobUrl = 'https://jennode.colanapps.in/job/Pipelinetest/build'
                    def jobToken = '11095bae9ca24926e6af9c6080ce74f226'
                    
                    def response = httpRequest(
                        contentType: 'APPLICATION_FORM',
                        httpMode: 'POST',
                        requestBody: "",
                        url: "${jobUrl}?token=${jobToken}"
                    )
                    
                    echo "Triggered build with response status: ${response.status}"
                }
                
                withSonarQubeEnv('SonarQube') {
                    sh "${mvnHome}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=${sonarProjectKey} -Dsonar.host.url=${sonarServerUrl}"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed :('
        }
    }
}
