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
        stage('Create kubeconfig file for jenkins user') {
            steps {
                withAWS(region: 'eu-central-1', credentials: 'neha-test') {
                    sh '''
                        aws eks --region eu-central-1 update-kubeconfig --name neha-cluster
                    '''
                    sh "kubectl get svc"
                }
            }
        }
    }
}
