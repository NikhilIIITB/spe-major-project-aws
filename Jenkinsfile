pipeline {
    agent any
    environment {
        GITHUB_REPO_URL = 'https://github.com/NikhilIIITB/spe-major-project-aws.git'
        EMAIL_TO = 'ankushpatil488@gmail.com'
        DOCKERHUB_USER = 'ankushpatil0125'
        EC2_DOCKER_IMAGE_NAME_REACT = 'ec2_frontend_image:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', url: "${GITHUB_REPO_URL}"
                }
            }
        }
        stage('Run Ansible EC2 Playbook') {
            steps {
                script {
                    ansiblePlaybook(
                        playbook: 'deploy-ec2.yml',
                        inventory: 'ec2-inventory.ini',
                        extras: '-e ansible_user=ubuntu -e ansible_python_interpreter=/usr/bin/python3.10',
                        credentialsId: '98a007f4-c8f5-49ed-b520-c2ec2524f97d',
                        sshExtraArgs: '-o StrictHostKeyChecking=no'
                    )
                }
            }
        }
    }
    post {
        success {
            emailext body: 'Build successful. Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
                    to: "${EMAIL_TO}", 
                    subject: 'Build successful in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        }
        failure {
            emailext body: 'Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
                    to: "${EMAIL_TO}", 
                    subject: 'Build failed in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        }
        unstable {
            emailext body: 'Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
                    to: "${EMAIL_TO}", 
                    subject: 'Unstable build in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        }
        changed {
            emailext body: 'Check console output at $BUILD_URL to view the results.', 
                    to: "${EMAIL_TO}", 
                    subject: 'Jenkins build is back to normal: $PROJECT_NAME - #$BUILD_NUMBER'
        }
    }
}
