pipeline {
  agent {
        label 'jenkins-fargate-terraform-docker'
  }
  environment {
        AWS_ACCESS_KEY_ID     = credentials('terraform-master-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('terraform-master-aws-secret-access-key')
  }
  stages {
    stage('checkout') {
      steps {
        git credentialsId: 'jose-luis.rodriguez3', url: 'https://github.com/joseluisrodriguez3/twotierterraform.git'
      }
    }
    stage('init') {
      steps {
        sh 'terraform init'
      }
    }
    stage('plan') {
      steps {
        # Credentials taken from environment directly
        sh 'terraform plan'
      }
    }
    stage('approval') {
      options {
        timeout(time: 1, unit: 'HOURS') 
      }
      steps {
        input 'approve the plan to proceed and apply'
      }
    }
    stage('apply') {
      steps {
        sh 'terraform apply -auto-approve'
        cleanWs()
      }
    }
  }
}
