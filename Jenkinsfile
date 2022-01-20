pipeline {
    agent any
    stages {
        stage ('Construção do Backend'){
            steps{
                bat 'mvn clean install -U -DskipTests=true'
            }
        }
    }
}