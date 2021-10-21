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
        stage('SCA'){
            steps{
                figlet 'Dependency-Check'
                sh 'mvn org.owasp:dependency-check-maven:check'
                
                archiveArtifacts artifacts: 'target/dependency-check-report.html', followSymlinks: false
            }
        }
        stage('Sonarqube'){
           steps{
               figlet 'SonarQube'
               script{
                   def scannerHome = tool 'Sonar'
                   
                   withSonarQubeEnv('sonarqube'){
                       sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ms-maven -Dsonar.sources=. -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
                   }
               }
           }
        }
        stage('DAST'){
            steps{
                figlet 'Owasp Zap DAST'
        		
        		script{
        		   env.DOCKER = tool "Docker"
        		   env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        		   env.TARGET = 'https://zero.webappsecurity.com/'
        	
        		    //sh '${DOCKER_EXEC} rm -f zap2'
        		    sh "${DOCKER_EXEC} pull owasp/zap2docker-stable"
                    sh '${DOCKER_EXEC} run --add-host="localhost:172.18.0.1" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8090:8090 -d owasp/zap2docker-stable zap.sh -daemon -port 8090 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:172.18.0.1" -v mnt/c/Users/claud/Documents/DevSecOps/Practica/jenkins_home/tools:/zap/wrk/ --rm -i owasp/zap2docker-stable zap-full-scan.py -t "https://zero.webappsecurity.com/" -I -r zap_full_report.html -l PASS'
                    
                    
        		   
        		   publishHTML([
        				    allowMissing: false,
        				    alwaysLinkToLastBuild: false,
        				    keepAll: false,
        				    reportDir: '/var/jenkins_home/tools/',
        				    reportFiles: 'zap_full_report.html',
        				    reportName: 'HTML Report',
        				    reportTitles: ''])
        		}
            }
        }
    }
}
