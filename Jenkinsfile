pipeline {
    agent any
    stages {
        stage('Lint HTML') {
            steps {
                sh 'tidy -q -e *.html'
            }
        }
        stage('Build') {
            steps {
                sh 'echo "Hello World" '
            }
        }
        stage('Build Docker Image') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
                sh '''
                    docker build -t nehashivhare/deployink8:$BUILD_ID .
                    '''
                 }
            }
        }
        stage('Push docker image to dockerhub') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
                    sh '''docker push nehashivhare/deployink8:$BUILD_ID'''
                }
            }
        }
        stage('Create kubeconfig file for jenkins user') {
            steps {
                withAWS(region: 'eu-central-1', credentials: 'neha-test') {
                    sh '''
                        aws eks --region eu-central-1 update-kubeconfig --name neha-cluster
                    '''
                    sh "kubectl get svc"
                    sh "kubectl get pod"
                }
            }
        }
    }
}
