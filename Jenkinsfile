pipeline {
    agent any

    environment {
        RESOURCE_GROUP = 'test'
        AKS_CLUSTER_NAME = 'myakscluster'
        ARM_SUBSCRIPTION_ID = credentials('azure-subscription-id')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/NarjesTaghlet/Nginx_Deployment.git'
                echo "Checkout completed"
            }
        }
        
        stage('Azure Login') {
            steps {
                script {
                    sh '''
                        az login --use-device-code
                        az account set --subscription $ARM_SUBSCRIPTION_ID
                    '''
                }
                echo "Azure Login completed"
            }
        }

        stage('Deploy NGINX Pod') {
            steps {
                withCredentials([file(credentialsId: 'AKS_KUBECONFIG', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl apply -f nginx-deployment.yaml
                    '''
                }
                echo "✅ NGINX Pod deployed"
            }
        }

        stage('Deploy NGINX Service') {
            steps {
                withCredentials([file(credentialsId: 'AKS_KUBECONFIG', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl apply -f nginx-service.yaml
                    '''
                }
                echo "✅ NGINX Service deployed"
            }
        }

        stage('Verify Deployment and Service') {
            steps {
                withCredentials([file(credentialsId: 'AKS_KUBECONFIG', variable: 'KUBECONFIG')]) {
                    // Vérification des Pods
                    sh 'kubectl get pods -o wide'
                    // Vérification des Deployments
                    sh 'kubectl get deployments'
                    // Vérification des Services
                    sh 'kubectl get services'
                }
            }
        }
    }
}
