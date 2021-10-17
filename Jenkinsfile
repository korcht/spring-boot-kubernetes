pipeline {
    agent any

    tools {
        maven 'Maven'
        nodejs 'NodeJs'
    }
  
    stages {
        stage('initial'){
            steps{
                figlet 'Inital'
                
             sh '''
              echo "PATH = ${PATH}"
              echo "M2_HOME = ${M2_HOME}"
              '''
            }
        }

        stage('Compile'){
            steps{
                figlet 'Compile'
                sh 'mvn clean compile -e'
            }
        }
        
        stage('Prueba'){
            steps{
                figlet 'Prueba'
                sh 'mvn clean prueba -e'
            }
        }
        
         
        stage('Sonarqube'){
           steps{
               figlet 'SonarQube'
               script{
                   def scannerHome = tool 'SonarQube Scanner'
                   
                   withSonarQubeEnv('sonarqube'){
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ms-maven -Dsonar.sources=. -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
                   }
               }
           }
          }
        
       
