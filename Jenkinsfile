pipeline {
    agent any

    environment {
        RESOURCE_GROUP = 'test'
        AKS_CLUSTER_NAME = 'myakscluster'
        //KUBECONFIG = credentials('aks-kubeconfig')
        ARM_SUBSCRIPTION_ID=credentials('azure-subscription-id')
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
                       #az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER_NAME --file $KUBECONFIG
                    '''
                }
                echo "Azure Login completed"
            }
        }
        /*stage('Deploy NGINX Pod') {
            steps {
                sh 'kubectl apply -f nginx-deployment.yaml'
            }
        }
*/
        stage('Deploy NGINX Pod') {
            steps {
                withCredentials([file(credentialsId: 'aks-kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl apply -f nginx-deployment.yaml
                    '''
                }
                echo "✅ NGINX Pod deployed"
            }
        }
        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods -o wide'
                sh 'kubectl get deployments'
            }
        }
    }
}