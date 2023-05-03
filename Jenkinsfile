pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="830593017048"
	AWS_DEFAULT_REGION="us-east-1"
	CLUSTER_NAME="best-cluster"
	SERVICE_NAME="nodejs-svc"
	TASK_DEFINITION_NAME="nodejs-app"
	DESIRED_COUNT="1"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "aws-admin-cred"
    }
	parameters{
	
	string(name:'AWS_Region', description: "Specify AWS Region", defaultValue: 'us-east-1')
        string(name: 'Image_RepoName', description: "name of the docker build", defaultValue: '')
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh 'npm install'
	  sh 'npm test -- --watchAll=false'
        }
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${Image_RepoName}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
			docker.withRegistry("https://" + REPOSITORY_URI + aws-admin-cred) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: 'aws-admin-cred') {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

