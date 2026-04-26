pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    stages {
        // REMOVED the manual CHECKOUT stage. Jenkins does this automatically!
        
        stage('Build') {
            steps {
                // Only use dir('demo') if your pom.xml is inside a folder named 'demo'
                dir('demo'){
                    bat 'mvn clean install'
                }
            }
        }
        stage('Test') {
            steps {
                dir('demo'){
                    bat 'mvn test'}
            }
        }
    }
}
                
