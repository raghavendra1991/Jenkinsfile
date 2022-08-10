// Declarative pipeline
pipeline {
    agent {
		label 'slave'
	}
    stages {
        stage('CleanUp WorkSpace') {
            steps {
                // Clean before build
                cleanWs()
                // We need to explicitly checkout from SCM here
                checkout scm
                echo "Building ${env.JOB_NAME}..."
            }
        }
	    stage('Build & Test') {
            agent {
	          dockerfile {
      	            filename 'Dockerfiletest'
   	                reuseNode true
	           }	        
	        }
	        steps {
	           echo "Building Image and Conatiner"
	           sh 'python3 test.py'
	        }
        }
	    stage ('Build Docker Image') {
			environment {
        		DOCKER_HUB_REPO = "raghaduvva/flaskapp"
        		DOCKERHUB_CREDENTIALS = credentials('docker-hub')
        		CONTAINER_NAME = "app"
			}
            steps {
                echo 'Building Docker Image'
                sh 'docker build -t $DOCKER_HUB_REPO:$BUILD_NUMBER .'       
            }
        } 
		stage('Create Containers') {
            steps {
                echo 'Creating Conatiner Testing Purpose'
                sh 'docker run -d --name $CONTAINER_NAME -p 5000:5000  $DOCKER_HUB_REPO:$BUILD_NUMBER'
            }
        }
		stage ('Smoke Test') {
            steps {
                script {
                    echo 'Smoke Test'
                    sh './script.sh'
                }
            }
        }	 
	}
}
