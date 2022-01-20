pipeline {
    agent any
    stages {
        stage ('Construcao do Backend'){
            steps{
                bat 'mvn clean install -U -DskipTests=true'
            }
        }
    }
}