pipeline {
    agent any 

    environment {
        // Define the VM credentials
        SSH_CREDENTIALS = credentials('SSHtoken') // Jenkins SSH credentials ID
        SECOND_VM_IP = '3.110.153.6' // IP address of the second VM
        DEPLOY_PATH = '/path/to/deploy/directory' // Path on the second VM where the application should be deployed
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    // Ensure Git is properly configured
                    if (!fileExists('.git')) {
                        echo 'Cloning repository...'
                        checkout([$class: 'GitSCM',
                            branches: [[name: '*/main']], // Change this to your branch if it's not 'main'
                            userRemoteConfigs: [[
                                url: 'https://github.com/sandeep23blr/Portfolio-Website.git',
                                credentialsId: 'gittoken' // Ensure this credential has access
                            ]]
                        ])
                    } else {
                        echo 'Repository already cloned, fetching latest changes...'
                        sh 'git fetch origin'
                        sh 'git checkout main' // Checkout the main branch
                        sh 'git pull origin main' // Pull the latest changes
                    }
                }
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building application...'
                // Adjust the path to the gradlew script if necessary
                sh 'chmod +x ./gradlew' // Make sure the script is executable
                sh './gradlew build' // Use the correct path if gradlew is in a subdirectory
            }
        }

        stage('Deploy Application') {
            steps {
                sshagent(['SSHtoken']) {
                    echo 'Deploying application to the second VM...'
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
            echo 'Deployment to the second VM was successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
