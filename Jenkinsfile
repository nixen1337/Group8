def commit_id
pipeline {
    agent any
    

    stages {
        stage('Preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {                        
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building....'
                sh 'mvn clean install'
                echo 'build complete'
            }
        }
        stage('Image Build') {
            steps {
                echo 'Building....'
                sh "'docker build -t position-simulator:${commit_id} ./'"
                echo 'build complete'
            }
        }
	stage('Image Push') {
		steps {
			echo 'pushing image.....'
			sh "'docker push position-simulator:${commit_id}'"
        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-position-simulator:release2|position-simulator:${commit_id}|' workloads.yaml"
                sh 'kubectl get all'
                sh 'kubectl apply -f .'
                sh 'kubectl get all'
                echo 'deployment complete'
                
            }
        }
    }
  }
 }
}