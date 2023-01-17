pipeline {
    agent any
    tools {
        terraform 'terraform'
    }
    stages {
        stage('Poll Code Repository') {
            steps {
                git credentialsId: 'jnks-pvt', url: 'git@github.com:Naveen-100/terraform-aks.git'
            }
        }
        stage('terraform format') {
            when {
                expression {action == 'apply'}
            }
            steps {
                script {
                    sh 'terraform fmt'
                }
            }
        }
        stage('terraform initialize') {
            when {
                expression {action == 'apply'}
            }
            steps{
                script {
                    sh 'terraform init'
                }
            }
        }
        stage('terraform validate') {
            when {
                expression {action == 'apply'}
            }
            steps{
                script {
                    sh 'terraform validate'
                }
            }
        }
        stage('terraform plan') {
            when {
                expression {action == 'apply'}
            }
            steps{
                script {
                    sh 'terraform plan'
                }
            }
        }
        stage('terraform apply') {
            when {
                expression {action == 'apply'}
            }
            steps{
                script {
                    sh 'terraform apply --auto-approve'
                }
            }
        }
        stage('configure kubectl') {
            when {
                expression {action == 'apply'}
            }
            steps{
                script {
                    sh 'az aks get-credentials --resource-group $(terraform output -raw resource_group_name) --name $(terraform output -raw kubernetes_cluster_name)'
                    }
            }
        }
        stage('deploy helm') {
            when {
                expression {action == 'apply'}
            }
            steps{
                sh'''
                    helm repo add bitnami https://charts.bitnami.com/bitnami
                    helm install my-nginx-release bitnami/nginx
                '''
            }
        }
        stage("check deployment"){
            when {
                expression{action == "apply"}
            }
            steps{
                sh '''
                    sleep 1m
                    kubectl get pods
                    kubectl get svc
                    curl $(kubectl get svc my-nginx-release -o yaml | grep -oP '(?<=ip: )[0-9].+')
                '''
            }
        }
        stage('terraform destroy') {
            when {
                expression {action == 'destroy'}
            }
            steps{
                script {
                        sh 'terraform destroy --auto-approve'
                }
            }
        }
    }
}