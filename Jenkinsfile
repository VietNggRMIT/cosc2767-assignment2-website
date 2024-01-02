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

        stage('Deploy Container') {
            steps {
                script {
                    // Find the WAR file path after Maven build
                    def warPath = sh(script: "find . -name '*.war' | head -n 1", returnStdout: true).trim()
                    // Publish over SSH to remote Docker server
                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'Docker_asm2',
                            transfers: [
                                // Transfer the WAR file to the remote server
                                sshTransfer(
                                    sourceFiles: warPath,
                                    removePrefix: 'target',
                                    remoteDirectory: '/home/dockeradmin'
                                )
                            ],
                            // SSH command to build and run the Docker container
                            execCommands: """
                                cd /home/dockeradmin
                                docker stop tomcat-server || true
                                docker rm tomcat-server || true
                                docker rmi tomcat-image-asm2 || true
                                docker build -t tomcat-image-asm2 .
                                docker run -d --name tomcat-server -p 8081:8080 tomcat-image-asm2
                            """
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
