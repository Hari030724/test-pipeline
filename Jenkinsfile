pipeline {
    agent any

    environment {
        // Define Maven and SonarQube related variables
        mvnHome = tool name: 'maven', type: 'maven'
        sonarProjectKey = 'Test-pipeline'
        sonarServerUrl = 'https://sonarqube.colanapps.in/' // Replace with your SonarQube server URL
    }

    stages {
        stage('Build and SonarQube Analysis') {
            steps {
                // Trigger the build job using HTTP POST request
                script {
                    def jobUrl = 'https://jennode.colanapps.in/job/Pipelinetest/build'
                    def jobToken = 'pipeline-test'
                    
                    def response = httpRequest(
                        contentType: 'APPLICATION_FORM',
                        httpMode: 'POST',
                        requestBody: "",
                        url: "${jobUrl}?token=${jobToken}"
                    )
                    
                    echo "Triggered build with response status: ${response.status}"
                }
                
                // Wait for the build to complete (optional)
                sleep time: 10, unit: 'SECONDS'
                
                // Run Maven build and SonarQube analysis
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
