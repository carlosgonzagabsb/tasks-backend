pipeline {
    agent any
    environment {
        LC_ALL = 'en_US.UTF-8'
      
    }
    stages {
        stage ('Construção do Backend'){
            steps{
                bat 'mvn clean install -U -DskipTests=true'
            }
        }
    }
}