pipeline {
    agent {
        label "builder"
    }

    stages {
        stage('Build and Test') {
            steps {
                sh"""
                mvn clean package
                """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}