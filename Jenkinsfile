# vim /etc/sudoers
# jenkins ALL=(ALL) NOPASSWD: ALL

pipeline {
    agent any
    tools{
        maven 'maven'
    }
    stages{  
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ygminds73/devops-cicd.git']]])
                sh 'mvn clean install'         //till install goals executed.. validate,compile,test,package,verify and install
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t youngminds73/devops-integration .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u youngminds73 -p ${dockerhubpwd}'

}
                   sh 'docker push youngminds73/devops-integration'
                }
            }
        }
        stage('EKS & kubectl configuration'){
            steps{
                script{
                    sh "aws eks update-kubeconfig --region ap-south-1 --name ankit_cluster"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
