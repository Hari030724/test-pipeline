pipeline {
        agent any
        stages {
          stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('colan-sonarqube-server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
          stage("Quality Gate") {
            steps {
            script {
           try {
                        withEnv(["PATH+MAVEN=${tool 'Maven Build'}/bin"]) {
                            sh 'mvn sonar:sonar -Dsonar.analysis.mode=publish'
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        echo "Error: ${e.message}"
                        throw e
                    }          }
     
        }
        }
                stage('Deploy') {
    steps {
        script {
            if (deploymentStatus != 'SUCCESS') {
                error 'Deployment failed'
                currentBuild.result = 'FAILURE'
                error 'Aborting pipeline due to deployment failure'
                return
            }
        }
    }
}
      
}
}
