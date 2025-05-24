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
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )
                ]) {
                    sh'''
                        echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        docker push "$REPO_NAME:$TAG"
                        docker logout
                    '''
                }

            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['deploy-server-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ec2-user@172.31.21.36 << 'EOF'
                            set -e  # Fail on error
                            docker rm -f project-1 || true
                            docker pull ${REPO_NAME}:${TAG}
                            docker run -d --name project-1 -p 8080:8080 ${REPO_NAME}:${TAG}
                    """
                }
            }
        }
    }
}