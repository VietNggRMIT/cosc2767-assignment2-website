// RMIT University Vietnam
// Course: COSC2767 Systems Deployment and Operations
// Semester: 2022C
// Assessment: Assignment 2
// Author: Nguyen Hoang Viet
// ID: s3926104
// Created  date: 20/02/2023
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

        stage('Build Docker Image') {
            steps {
                script {
                    sh "cd /home/dockeradmin"
                    sh "docker build -t tomcat-image-asm2 ."
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Stop the currently running container if it exists
                    sh "docker stop tomcat-server || true"
                    // Remove the stopped container
                    sh "docker rm tomcat-server || true"
                    // Run a new container from the built image
                    sh "docker run -d --name tomcat-server -p 8081:8080 tomcat-image-asm2"
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
