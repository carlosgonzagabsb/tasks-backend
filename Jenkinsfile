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
                sleep(5)
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
    }
}