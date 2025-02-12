pipeline {
    agent any
    stages {
        stage ('Construcao do Backend'){
            steps{
                bat 'mvn clean install -U -DskipTests=true'
            }
        }
        stage ('Testes Unitarios'){
            steps{
                bat 'mvn test'
            }
        }
         stage ('Analise Sonar'){
             environment{
                 scannerHome = tool 'SONAR_SCANNER'
             }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=d0adc32263ce4680f44de29ebdceb08d58e82a8f -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage ('Quality Gate'){
            steps {
                sleep(20)
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Backend'){
            steps{
               deploy adapters: [tomcat8(credentialsId: 'login_tomcat', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API REST Test'){
            steps{
                dir('api-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/carlosgonzagabsb/api-test.git'
                    bat 'mvn test' 
                }
            }
        }
         stage ('Deploy Fontend'){
            steps{
                dir('frontend'){
                     git credentialsId: 'github_login', url: 'https://github.com/carlosgonzagabsb/tasks-frontend.git'
                     bat 'mvn clean install -U -DskipTests=true'
                     deploy adapters: [tomcat8(credentialsId: 'login_tomcat', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-frontend', war: 'target/tasks.war'    
                }
              
            }
        }
        stage('Deploy Prod') {
            steps {
                bat 'docker-compose build'
                bat 'docker-compose up -d'
            }
        }
        stage ('Construcao Teste Funcional'){
            steps{
                dir('teste-funcional'){
                     git credentialsId: 'github_login', url: 'https://github.com/carlosgonzagabsb/teste-funcional.git'
                     bat 'mvn clean install -U -DskipTests=true'
                     
                }
              
            }
        }
        stage ('Teste-funcional'){
            steps{
                dir('C:/Users/c1202836/.jenkins/workspace/Pipeline/teste-funcional/target'){
                     bat 'java -cp teste-funcional-0.0.1-SNAPSHOT.jar br.com.selenium.RunTeste'    
                }  
	         }
        }
        
    }
    post {
        always {
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
            archiveArtifacts artifacts: 'target/tasks-backend.war, frontend/target/tasks.war', onlyIfSuccessful: true
        }
        unsuccessful {
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'wcaquino+jenkins@gmail.com'
        }
        fixed {
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build is fine!!!', to: 'wcaquino+jenkins@gmail.com'
        }
    }
}
