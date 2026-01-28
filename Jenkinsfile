pipeline {
    agent any
    tools {
        maven 'maven'
    }

    stages {
        stage('Create Jar File') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
