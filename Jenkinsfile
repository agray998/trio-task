pipeline {
	agent any
	environment {
		PASSWORD = credentials('password')
	}
	stages {
		stage('build and push') {
			steps {
				sh 'docker-compose build'
				sh 'docker-compose push'
			}
		}
		stage('clean') {
			steps {
				sh 'docker rmi gcr.io/lbg-mea-11/trio-db:v2'
				sh 'docker rmi gcr.io/lbg-mea-11/trio-app:v2'
			}
		}
		stage('deploy to cluster') {
			steps {
				sh '''
				sed -e 's,{{PASSWORD}},'${PASSWORD}',g;' mysql-creds.yaml | kubectl apply -f -
				kubectl apply -f nginx-config.yaml
				kubectl apply -f db.yaml
				kubectl apply -f trio-deployment.yaml
				kubectl apply -f nginx-pod.yaml
				'''
			}
		}
	}
}
