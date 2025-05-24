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
                sshagent(credentials: ['deploy-server-credentials']) {
                sh '''
                    scp -o StrictHostKeyChecking=no ./target/Web*.war ec2-user@172.31.21.36:/opt/tomcat/webapps/ROOT.war
                '''
    }
            }
        }
    }
}