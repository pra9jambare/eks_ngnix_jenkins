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

        stage('AWS Identity Check') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh '''
                    aws sts get-caller-identity
                    '''
                }
            }
        }

        stage('Configure EKS Access') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-cred']
                ]) {
                    sh '''
                    aws eks update-kubeconfig \
                      --region $AWS_REGION \
                      --name $CLUSTER_NAME
                    '''
                }
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

    post {
        success {
            echo "✅ Deployment successful to EKS"
        }
        failure {
            echo "❌ Deployment failed - check logs"
        }
    }
}
