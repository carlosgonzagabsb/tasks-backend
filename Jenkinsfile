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
            timeout(time: 1, unit: 'MINUTES') {
            def qg = waitForQualityGate()
                if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
        }
    }
}