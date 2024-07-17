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
                     writeFile file: 'src/main/java/com/example/BuggyClass.java', text:
            
                    public class BuggyClass {
                        public void buggyMethod() {
                            String password = "password"; 
                            System.out.println(password); }
                  def qg = waitForQualityGate abortPipeline: false, credentialsId: 'colan-sonaqube-server-global-access-token'
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    } else {
                        echo "Quality gate passed: ${qg.status}"
                    } 
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
