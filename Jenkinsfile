pipeline {
    agent any

    stages {
        stage('BuildApp') {
            steps {
                script {
                    echo 'Building the application...'
                }
            }
        }
        
        stage('Configure Kubeconfig and Test Connectivity') {
            steps {
                // Inject AWS credentials to allow aws eks update-kubeconfig to work.
                withCredentials([
                    string(credentialsId: 'jenkins_aws_access_key_id', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'jenkins_aws_secret_access_key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                        # Verify AWS CLI version
                        aws --version
                        
                        # Set the AWS region environment variable
                        export AWS_DEFAULT_REGION=ap-southeast-1
                        
                        # Update kubeconfig to allow kubectl to connect to your EKS cluster.
                        aws eks update-kubeconfig --region ap-southeast-1 --name demo-cluster
                        
                        # Test connectivity by listing the cluster nodes.
                        kubectl get nodes
                    '''
                }
            }
        }
        
        stage('Deploy') {
            environment {
                // Inject credentials for deployment operations.
                AWS_ACCESS_KEY_ID     = credentials('jenkins_aws_access_key_id')
                AWS_SECRET_ACCESS_KEY = credentials('jenkins_aws_secret_access_key')
                AWS_DEFAULT_REGION    = 'ap-southeast-1'
            }
            steps {
                script {
                    echo 'Deploying docker image to EKS cluster...'
                    // Create an Nginx deployment in the EKS cluster.
                    sh 'kubectl create deployment nginx-deployment --image=nginx'
                }
            }
        }
    }
}
