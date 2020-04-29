pipeline {
environment {
registry = "nileshbhadana/parse-server"
registryCredential = 'dockerhub_id_nilesh'
dockerImage = ''
NAMESPACE= "parseapp2"
}
agent any
stages {

	stage('helm list') {
    		steps{
			sh "kubectl get pods --namespace parseapp"
		}
	}
}
}
