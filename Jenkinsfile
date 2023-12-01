pipeline {
    agent any
    environment {
        registry = "241916522557.dkr.ecr.ap-south-1.amazonaws.com/my-ecr-repo"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], userRemoteConfigs: [[url: 'https://github.com/YashodaRG/java-example.git']]])     
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
	stage('Pushing to ECR') {
     steps{ 
	    script {
		withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'github']]){
           sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 241916522557.dkr.ecr.ap-south-1.amazonaws.com'
           sh 'docker push 241916522557.dkr.ecr.ap-south-1.amazonaws.com/my-ecr-repo:latest'
         }
        }
      }
	 }
	//stage('Docker Run') {
          //steps{
              //script {
                  // sh 'docker run -d -p 8080:8080 241916522557.dkr.ecr.ap-
                   // south-1.amazonaws.com/my-ecr-repo:latest'
               // }
         // }
     // }
                 
    //stage('Deploy to k8s'){
            //steps{
                //script{
                    //kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'Kubeconfpwd')
            //}
      //}
    //}
	    stage('Deploy App on k8s') {
      steps {
            sshagent(['k8s']) {
            sh "scp -o StrictHostKeyChecking=no deploymentservice.yaml ubuntu@172.31.37.64:/home/ubuntu"
            script {
                try{
                    sh "ssh ubuntu@172.31.37.64 kubectl apply -f ."
                }catch(error){
                    sh "ssh ubuntu@172.31.37.64 kubectl create -f ."
            }
}
        }
      
    }
    }
}
    }

