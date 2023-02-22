pipeline {
    agent any
    stages {
        stage('Build Flask App') {
            steps {
                sh '''
                docker build -t eu.gcr.io/lbg-cloud-incubation/swells-flask-lbg-demo:latest -t eu.gcr.io/lbg-cloud-incubation/swells-flask-lbg-demo:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Build Custom NGINX') {
            steps {
                sh '''
                ls
                cd ./nginx
                docker build -t eu.gcr.io/lbg-cloud-incubation/swells-nginx-lbg-demo:latest -t eu.gcr.io/lbg-cloud-incubation/swells-nginx-lbg-demo:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Push Images') {
            steps {
                sh '''
                docker push eu.gcr.io/lbg-cloud-incubation/swells-flask-lbg-demo:latest
                docker push eu.gcr.io/lbg-cloud-incubation/swells-flask-lbg-demo:build-$BUILD_NUMBER
                docker push eu.gcr.io/lbg-cloud-incubation/swells-nginx-lbg-demo:latest
                docker push eu.gcr.io/lbg-cloud-incubation/swells-nginx-lbg-demo:build-$BUILD_NUMBER
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                cd ./kubernetes
                kubectl apply -f .
                kubectl rollout restart deployment flask-deployment
                kubectl rollout restart deployment nginx-deployment
                '''
            }
        }
    }
}