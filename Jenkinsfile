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
                cd ./ngnix
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
        stage('code branch Stop and Clean and deal with multiply namespaces with sed substition in yaml') {
            steps {
                script {
			        if ("${GIT_BRANCH}" == 'origin/main') {
				        sh '''
				        cd kubernetes
                        sed -e 's,{{ns}},production,g;' *.yml | kubectl apply -f -
                        

				        '''
			        } else if ("${GIT_BRANCH}" == 'origin/development') {
				        sh '''
				        cd kubernetes
                        sed -e 's,{{ns}},development,g;' *.yml | kubectl apply -f -
				        '''
			        }
		        }
            }
        }
        stage('Deploy') {
            steps {
                
                script {
			        if ("${GIT_BRANCH}" == 'origin/main') {
				        sh '''
				        kubectl rollout restart deployment --namespace=production python-app
                        kubectl rollout restart deployment --namespace=production nginxproxy
                        

				        '''
			        } else if ("${GIT_BRANCH}" == 'origin/development') {
				        sh '''
				        kubectl rollout restart deployment --namespace=development python-app
                        kubectl rollout restart deployment --namespace=development nginxproxy
				        '''
			        }
		        }
                
                
            }
        }
    }
}