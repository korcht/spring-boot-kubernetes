pipeline {
    agent any

    tools {
        maven 'Maven'
        nodejs 'NodeJS'
    }
  stages {
   stage ('Initial') {
            steps {
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
        stage ('Compile') {
            steps {
                 sh 'mvn clean compile -e'
            }
        }
        stage('Sonarqube'){
           steps{
               figlet 'SonarQube'
               script{
                   def scannerHome = tool 'Sonar'
                   
                   withSonarQubeEnv('sonarqube'){
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=t5sca -Dsonar.sources=. -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
                   }
               }
           }
        }
    }
}
