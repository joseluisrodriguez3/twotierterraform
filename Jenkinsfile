pipeline {
  agent none
  environment {
        AWS_ACCESS_KEY_ID     = credentials('terraform-master-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('terraform-master-aws-secret-access-key')
  }
  stages {
    stage('checkout') {
      steps {
        checkout scm
        sh 'docker pull hashicorp/terraform:light'
      }
    }
    stage('init') {
      agent any
      steps {
        sh 'docker run -w /app -v /root/.aws:/root/.aws -v `pwd`:/app hashicorp/terraform:light init'
      }
    }
    stage('plan') {
      agent {
        label 'jenkins-fargate-terraform-docker'
      }
      steps {
        sh 'docker run -w /app -v /root/.aws:/root/.aws -v `pwd`:/app hashicorp/terraform:light plan'
      }
    }
    stage('approval') {
      agent {
        label 'jenkins-fargate-terraform-docker'
      }
      options {
        timeout(time: 1, unit: 'HOURS') 
      }
      steps {
        input 'approve the plan to proceed and apply'
      }
    }
    stage('apply') {
      agent {
        label 'jenkins-fargate-terraform-docker'
      }
      steps {
        sh 'docker run -w /app -v /root/.aws:/root/.aws -v `pwd`:/app hashicorp/terraform:light apply -auto-approve'
        cleanWs()
      }
    }
  }
}
