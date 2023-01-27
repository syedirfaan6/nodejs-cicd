pipeline {
environment {
AWS_ACCOUNT_ID="098324025508"
AWS_DEFAULT_REGION="ap-south-1"
IMAGE_REPO_NAME="nodejs-cicd"
IMAGE_TAG="latest"
REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
}
  agent any
  stages {
    
    stage('git clone'){
      steps{
        script{
          git branch: 'main', credentialsId: 'git', url: 'https://github.com/suryatinku/nodejs-cicd.git'}}}
         
    stage('build image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
     stage('login into ecr') {
      steps{
        script {
          withCredentials([<object of type com.cloudbees.jenkins.plugins.awscredentials.AmazonWebServicesCredentialsBinding>]) 
        {
          sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"}
          }
        }
      }
 
stage('Push Image') {
      steps{
         script {
             sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
             sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"

          }
        }
      }    
    
    stage('Deploy to EKS'){
      steps{
          script {
            kubeconfig(credentialsId: 'eks', serverUrl: 'https://4757D7080B9915CAB42D091D38A21EAE.gr7.ap-south-1.eks.amazonaws.com') {
             sh 'kubectl apply -f k8s.yaml'
}          
      }
    }
  }
    
  }
}
