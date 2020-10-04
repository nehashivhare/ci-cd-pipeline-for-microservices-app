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
                    docker build -t nehashivhare/deployink8:latest .
                    '''
                 }
            }
        }
        stage('Push docker image to dockerhub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url:"") {
                    sh '''docker push nehashivhare/deployink8:latest'''
                }
            }
        }
        stage('Create kubeconfig file for jenkins user') {
            steps {
                withAWS(region: 'us-east-2', credentials: 'neha-test') {
                    // creates or updates config file
                    sh ''' aws eks --region us-east-2 update-kubeconfig --name final-project-neha-cluster'''
                    sh "kubectl get svc"
                    sh "kubectl config use/context arn:aws:eks:us-east-2:945235147511:cluster/final-project-neha-cluster"
                    // roll out updates in production
                    sh "kubectl set image deployment/microservices-in-k8 microservices-in-k8=nehashivhare/deployink8:latest"
                    // deploy container to kubernetes
                    sh "kubectl apply -f deployment/deploy-app-in-kubernetes.yaml"
                    sh "kubectl get service/microservices-in-k8"
                }
            }
        }
    }
}
