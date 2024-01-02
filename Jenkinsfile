// RMIT University Vietnam
// Course: COSC2767 Systems Deployment and Operations
// Semester: 2022C
// Assessment: Assignment 2
// Author: Nguyen Hoang Viet
// ID: s3926104
// Created date: 20/02/2023
// Last modified: 28/12/2923
// Acknowledgement: 

pipeline {
    agent any

    tools {
        maven 'maven-3.9.5'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/VietNggRMIT/cosc2767-assignment2-website.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Transfer & Deploy') {
            steps {
                script {
                    // Define the full path to the WAR file
                    def warFullPath = "/var/lib/jenkins/workspace/BuildAndDeployPipeline/target/assignment-2.war"
                    // Publish over SSH to transfer the WAR file
                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'Docker_asm2',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: warFullPath,
                                    removePrefix: '/var/lib/jenkins/workspace/BuildAndDeployPipeline/target/',
                                    remoteDirectory: '/home/dockeradmin/',
                                    execCommand: """
                                        cd /home/dockeradmin/
                                        ls -lah  # Check if the WAR file is present
                                        # Stop and remove the old container
                                        docker stop tomcat-server || true
                                        docker rm tomcat-server || true
                                        # Remove the previous image
                                        docker rmi tomcat-image-asm2 || true
                                        # Build the new Docker image
                                        docker build -t tomcat-image-asm2 .
                                        # Run the new Docker container
                                        docker run -d --name tomcat-server -p 8081:8080 tomcat-image-asm2
                                    """
                                )
                            ]
                        )
                    ])
                }
            }
        }

    }

    post {
        always {
            echo 'Pipeline execution complete.'
        }
    }

    triggers {
        pollSCM('* * * * *')
    }
}
