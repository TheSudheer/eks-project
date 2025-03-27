pipeline {
    agent any
    
    stages {
        stage('BuildApp') {
            steps {
                script {
                    echo 'Running application build script...'
                }
            }
        }
        stage('BuildImage') {
            steps {
                script {
                    echo 'Running Docker image build script...'
                }
            }
        }
        stage ('Deploy') {
            environment {
                // Retrieve AWS credentials from Jenkins credentials store
                AWS_ACCESS_KEY_ID     = credentials('jenkins_aws_access_key_id')
                AWS_SECRET_ACCESS_KEY = credentials('jenkins_aws_secret_access_key')
                AWS_DEFAULT_REGION    = 'ap-southeast-1' // Set your desired AWS region
            }
            steps {
                script {
                    echo 'Deploying docker image to EKS cluster...'
                    // Execute kubectl command to create an Nginx deployment
                    sh 'kubectl create deployment nginx-deployment --image=nginx'
                }
            }
        }
    }
}

