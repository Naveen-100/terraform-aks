pipeline {
    agent any
    stages {
        stage('pull the code') {
            steps {
               git branch: 'main', url: 'https://github.com/Naveen-100/terraform-aks.git'
            }
        }
        stage('Terraform version check') {
            steps {
                sh "terraform -v"
            }
        }
        stage('Initialize terraform') {
            steps {
                sh "terraform init"
            }
        }
        stage('Plan') {
            steps {
                sh "terraform plan"
            }
        }
        stage('Deploy the resoources') {
            steps {
                sh '''
                    terraform apply -auto-approve
                '''
            }
        }
        stage("Login to aks"){
            steps{
               sh 'az aks get-credentials --resource-group example-aks --name example-aks1' 
            }
        }
        stage("Create the nginx chart"){
            steps{
               sh 'helm create nginx' 
            }
            
        }
        stage("Install the nginx"){
            steps{
                sh 'helm install nginx ./nginx/ --set service.type=LoadBalancer'
            }
            
        }
    }
}