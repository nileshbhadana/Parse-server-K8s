pipeline {
environment {
registry = "nileshbhadana/parse-server"
registryCredential = 'dockerhub_id_nilesh'
dockerImage = ''
NAMESPACE= "parseapp"
}
agent any
stages {

	stage('Cloning our Git') {
		steps {
			git "https://github.com/nileshbhadana/Parse-server-K8s.git"
		}
	}


	stage('Building our image') {
		steps{
			script {
				dockerImage = docker.build(registry + ":$BUILD_NUMBER",'-f ./deploy/docker/Dockerfile .')
			}
		}
	}
	
	stage('Deploy our image') {
		steps{
			script {
				docker.withRegistry( '', registryCredential ) {
				dockerImage.push()
				}
			}
		}
	}

	stage('Cleaning up') {
		steps{
			sh ""
		}
	}

	stage('helm list') {
    		steps{
			sh "helm ls"
		}
	}

	stage('Deployment') {
		steps{
    			sh "helm upgrade --install --atomic --wait --timeout 300 mongo ./deploy/helm/mongo-db/ --namespace ${NAMESPACE}"
    			sh "helm upgrade --install --atomic --wait --timeout 300 parse-server ./deploy/helm/parse-server/ --set image.tag=${BUILD_NUMBER},image.repository=${registry}  --namespace ${NAMESPACE}"
		}
	}

	stage('Image Rollout'){
    		steps{
			sh ("kubectl rollout status deployment/parse-server -n ${NAMESPACE}")
		}
	}
}

}
