pipeline {
    agent any
    environment {
        GITHUB_REPO_URL = 'https://github.com/ankushpatil0125/SPE-Major-Project.git'
        EMAIL_TO = 'ankushpatil488@gmail.com'
        DOCKERHUB_USER = 'ankushpatil0125'
        EC2_DOCKER_IMAGE_NAME_REACT = 'ec2_frontend_image:latest'
        // EC2_DOCKER_IMAGE_NAME_SPRING = 'ec2_backend_image:latest'
        // EC2_DOCKER_IMAGE_NAME_FLASK  = 'ec2_flask_image:latest'
    }

    stages {
        stage('Checkout'){
            steps {
                script {
                    // Checkout the code from the GitHub repository
                    git branch: 'main', url: "${GITHUB_REPO_URL}"
                }
            }
        }
        stage('Check TEST cases') {
        steps {
            script {
                sh 'mvn -f SpringBackend/ test'
            }
        }
        }
        stage('Build Maven Project') {
            steps {
                script {
                    sh 'mvn -f SpringBackend/pom.xml clean install -DskipTests'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKERHUB_USER}/${EC2_DOCKER_IMAGE_NAME_REACT} -f ReactFrontend/Dockerfile ."
                    // sh "docker build -t ${DOCKERHUB_USER}/${EC2_DOCKER_IMAGE_NAME_SPRING} -f SpringBackend/Dockerfile ."	
                    // sh "docker build -t ${DOCKERHUB_USER}/${EC2_DOCKER_IMAGE_NAME_FLASK} -f FlaskBackend/Dockerfile ."
                }
            }
        }
        stage('Push Docker Images') {
            steps {
                script{
                    docker.withRegistry('', 'DockerHubCred') {
                    sh 'docker push ankushpatil0125/ec2_frontend_image'
                    // sh 'docker push ankushpatil0125/ec2_backend_image'
                    // sh 'docker push ankushpatil0125/ec2_flask_image'
                    }
                 }
            }
        }
	    stage('Run Ansible EC2 Playbook') {
            steps {
                script {
                    ansiblePlaybook(
                    playbook: 'deploy-ec2.yml',
                        inventory: 'ec2-inventory.ini',
                        extras: '-e ansible_user=ubuntu -e ansible_python_interpreter=/usr/bin/python3',
                        credentialsId: '98a007f4-c8f5-49ed-b520-c2ec2524f97d',
                        sshExtraArgs: '-i /home/ankushpatil488/Downloads/SPE_WebServer_Key.pem'
                    )
                }
            }
        }
    }
    post {
        success {
            emailext body: 'Build successful.Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
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
