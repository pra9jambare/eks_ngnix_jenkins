pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        CLUSTER_NAME = 'eks-fargate-probe-cluster'
        GIT_REPO = "https://github.com/pra9jambare/eks_ngnix_jenkins/"
    }

    stages {

        stage('Checkout Code') {
            steps {
                  git credentialsId: '558df15d-7b93-44f8-8966-f423c8716be0',
                    url: "${GIT_REPO}",
                    branch: 'main'
            }
        }

        stage('Configure EKS Access') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                  --region ap-south-1 \
                  --name eks-fargate-probe-cluster
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
