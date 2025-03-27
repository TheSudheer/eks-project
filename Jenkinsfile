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
                withCredentials([
                    string(credentialsId: 'jenkins_aws_access_key_id', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'jenkins_aws_secret_access_key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                        # Check if AWS CLI is installed; if not, download and install it.
                        if ! command -v aws > /dev/null; then
                            echo "AWS CLI not found. Installing AWS CLI..."
                            curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                            unzip awscliv2.zip
                            sudo ./aws/install
                        else
                            echo "AWS CLI is already installed."
                        fi
                        
                        # Verify the installation by outputting the version.
                        aws --version
                        
                        # Set the AWS region environment variable
                        export AWS_DEFAULT_REGION=ap-southeast-1
                        
                        # Update kubeconfig so kubectl can talk to your EKS cluster.
                        aws eks update-kubeconfig --region ap-southeast-1 --name demo-cluster
                        
                        # Test connectivity by listing the cluster nodes.
                        kubectl get nodes
                    '''
                }
            }
        }
        
        stage('Deploy') {
            environment {
                // Inject credentials for deployment context as well.
                AWS_ACCESS_KEY_ID     = credentials('jenkins_aws_access_key_id')
                AWS_SECRET_ACCESS_KEY = credentials('jenkins_aws_secret_access_key')
                AWS_DEFAULT_REGION    = 'ap-southeast-1'
            }
            steps {
                script {
                    echo 'Deploying docker image to EKS cluster...'
                    // Create a deployment in the EKS cluster using kubectl.
                    sh 'kubectl create deployment nginx-deployment --image=nginx'
                }
            }
        }
    }
}
