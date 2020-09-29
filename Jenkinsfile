pipeline {
    agent any
    stages {
        stage('Create docker image') {
            steps {
                script {
                    echo 'Create Dockerfile ...'
                    cd ./2/debian-10/
                    sh 'docker build -t registry-192.168.1.38.nip.io/bitnami/jenkins:249.1 .'
                }
            }
        }
        stage('Push docker image') {
            steps {
                script {
                    echo 'Push Docker Image ...'
                    sh 'docker push registry-192.168.1.38.nip.io/bitnami/jenkins:249.1'
                }
            }
        }

    }
    post {
        always {
            cleanWs()
        }
    }
}