pipeline {
    agent {
        label "builder"
    }
    environment{
        REPO_NAME="delightfela/project-1"
        TAG=sh(script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout | tr -d "-"', returnStdout: true).trim()
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh"""
                echo $REPO_NAME:$TAG
                docker build -t $REPO_NAME:$TAG .
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(CredentialsId:'docker-hub-credentials', usernameVariable:'DOCKERHUB_USER', passwordVariable:'DOCKERHUB_PASS')]){
                sh"""
                echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                docker push $REPO_NAME:$TAG
                """
                }

            }
        }

        // stage('Deploy') {
        //     steps {
        //         sshagent(credentials: ['deploy-server-credentials']) {
        //         sh '''
        //             scp -o StrictHostKeyChecking=no ./target/Web*.war ec2-user@172.31.21.36:/opt/tomcat/webapps/ROOT.war
        //         '''
        //         }
        //     }
        // }
    }
}