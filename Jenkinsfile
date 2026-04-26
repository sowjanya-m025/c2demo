pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }
    stages {
        // REMOVED the manual CHECKOUT stage. Jenkins does this automatically!
        
        stage('Build') {
            steps {
                // Only use dir('demo') if your pom.xml is inside a folder named 'demo'
                dir('demo'){
                    sh 'mvn clean install'
                }
            }
        }
        stage('Test') {
            steps {
                dir('demo'){
                    sh 'mvn test'}
            }
        }
    }
}
                
