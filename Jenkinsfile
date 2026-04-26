pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    stages {

        stage('CHECKOUT') {
            steps {
                git 'https://github.com/sowjanya-m025/demo.git'
            }
        }

        stage('Build') {
            steps {
                dir('demo') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Test') {
            steps {
                dir('demo') {
                    sh 'mvn test'
                }
            }
        }
    }
}
