pipeline {
properties([
        parameters([
        string(defaultValue: "master", description: 'Which Git Branch to clone?', name: 'GIT_BRANCH'),
        string(defaultValue: "parseapp", description: 'Namespace for setup application', name: 'NAMESPACE'),
        string(defaultValue: "1", description: 'pod count', name: 'replicacount'),
        string(defaultValue: "nileshbhadana", description: 'Environment name', name: 'GIT_ORG'),
        string(defaultValue: "Parse-server-K8s", description: 'Which Git Repo to clone?', name: 'GIT_APP_REPO'),
        choice(name: 'action', choices: "build", description: 'choose for build and rollback')
        ])
])

environment {
registry = "nileshbhadana/parse-server"
registryCredential = 'dockerhub_id_nilesh'
dockerImage = ''
}
agent any
stages {

	stage('Cloning our Git') {
		steps {
			git "https://github.com/$GIT_ORG/$GIT_APP_REPO .git"
		}
	}


	stage('Building our image') {
		steps{
			script {
				dockerImage = docker.build registry + ":$BUILD_NUMBER"
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
			sh "docker rmi $registry:$BUILD_NUMBER"
		}
	}

	stage('helm list') {
    		sh "helm ls"
	}

	stage('Deployment') {
    		sh "helm upgrade --install --atomic --wait --timeout 300 mongo ./deploy/helm/mongo-db/ --namespace ${NAMESPACE}"
    		sh "helm upgrade --install --atomic --wait --timeout 300 parse-server ./deploy/helm/parse-server/ --set image.tag=${BUILD_NUMBER},replicaCount=${replicacount},image.repository=${registry}  --namespace ${NAMESPACE}"
	}

	stage('Image Rollout'){
    		sh ("kubectl rollout status deployment/parse-server -n ${NAMESPACE}")
	}
}

}
