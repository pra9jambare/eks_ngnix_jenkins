pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        CLUSTER_NAME = 'demo-eks'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/eks-nginx.git'
            }
        }

        stage('Configure EKS Access') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                  --region $AWS_REGION \
                  --name $CLUSTER_NAME
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }
}
