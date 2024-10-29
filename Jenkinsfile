pipeline {
    agent any 

    environment {
        // Define the VM credentials
        SSH_CREDENTIALS = credentials('SSHtoken') // Add your Jenkins SSH credentials ID
        SECOND_VM_IP = '3.110.153.6' // Replace with the actual IP address of the second VM
        DEPLOY_PATH = '/path/to/deploy/directory' // Path on the second VM where the application should be deployed
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone the GitHub repository
                git 'https://github.com/sandeep23blr/Portfolio-Website.git'
            }
        }

        stage('Build Application') {
            steps {
                // Assuming you have a build step, such as Maven or Gradle; adjust accordingly
                sh './gradlew build' // Replace with the appropriate build command
            }
        }

        stage('Deploy Application') {
            steps {
                // Copy built artifacts to the second VM
                sshagent(['your-ssh-credentials-id']) {
                    sh """
                        scp -o StrictHostKeyChecking=no -r build/libs/*.jar ${SECOND_VM_IP}:${DEPLOY_PATH}
                        ssh -o StrictHostKeyChecking=no ${SECOND_VM_IP} "cd ${DEPLOY_PATH} && java -jar *.jar"
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment to second VM was successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
