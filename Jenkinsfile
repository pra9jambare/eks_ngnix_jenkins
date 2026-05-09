pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        CLUSTER_NAME = 'eks-fargate-probe-cluster'
        GIT_REPO = "https://github.com/pra9jambare/eks_ngnix_jenkins"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git credentialsId: '558df15d-7b93-44f8-8966-f423c8716be0',
                    url: "${GIT_REPO}",
                    branch: 'main'
            }
        }

        stage('Verify AWS Credentials') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh '''
                    echo "🔐 Checking AWS identity..."
                    aws sts get-caller-identity
                    '''
                }
            }
        }

        stage('Configure EKS') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh '''
                    set -e

                    echo "⚙️ Updating kubeconfig for EKS cluster..."
                    aws eks update-kubeconfig \
                        --region $AWS_REGION \
                        --name $CLUSTER_NAME

                    echo "🧪 Testing cluster access..."
                    kubectl get nodes
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh '''
                    set -e

                    echo "🚀 Deploying NGINX to EKS..."

                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    echo "⏳ Waiting for deployment rollout to complete..."
                    kubectl rollout status deployment/nginx-deployment --timeout=120s
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                echo "📦 Pods:"
                kubectl get pods

                echo "🌐 Services:"
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful to EKS"
        }
        failure {
            echo "❌ Deployment failed — check AWS credentials or kubeconfig setup"
        }
    }
}
