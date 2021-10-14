#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage('Prepare') {
            steps {
                sh "ansible --version"
                sh "python --version"
            }
        }
        stage('Deploy Application') {
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: "ec2-ssh", keyFileVariable: "SSH_KEY", usernameVariable: "SSH_USER")]) {
                sh "export ANSIBLE_HOST_KEY_CHECKING=False && \
                    ansible-playbook --private-key ${SSH_KEY} \
                    -u ${SSH_USER} \
                    -e ANSIBLE_HOST_KEY_CHECKING=False \
                    -i ansible/inventory \
                    ansible/playbook.yml"
                }
            }
        }    
    }
     post {
         success {
             echo 'success'
             emailext body: 'Build successful',
                 subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}\ build ${env.BUILD_NUMBER}\n More info at:
             ${env.BUILD_URL}",
                 recipientProviders: [requestor()],  
                 to: 'oliynyk99@gmail.com'
             )
         }
         failure {
             emailext (
                 body: 'Build failed',
                 subject: "Jenkins Build ${currentBuild.currentResult}: Job \"${env.JOB_NAME}\"",
                 recipientProviders: [requestor()],  
                to: 'oliynyk99@gmail.com'
             )
         }
     }
}
