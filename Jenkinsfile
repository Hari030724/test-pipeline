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
           sh 'mvn sonar:sonar -Dsonar.analysis.mode=publish'

          }
     
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
