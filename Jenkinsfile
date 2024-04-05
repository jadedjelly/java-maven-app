#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/jadedjelly/jenkins-shared-library.git',
    credentialsID: 'github-creds'
    ]
)

pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }

    enviroment {
        IMAGE_NAME = 'jadedjelly/mod9demo-app:java-maven-1.0'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo 'building the docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    echo 'deploying docker image to EC2...'
                    def dockerCmd = "docker run -p 8080:8080 -d ${IMAGE_NAME}"
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@18.202.21.18 ${dockerCmd}"
                    }
                }
            }
        }
//        }
//        stage('commit version update'){
//            steps {
//                script {
//                    echo "Increment version below commented out"
//                    withCredentials([usernamePassword(credentialsId: 'github-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]){
//                        sh 'git remote set-url origin https://$USER:$PASS@github.com/jadedjelly/java-maven-app.git'
//                        sh 'git add .'
//                        sh 'git commit -m "ci: version bump"'
//                        sh 'git push origin HEAD:jenkins-jobs'
//                    }
//                  }
//            }
//        }
//    }
    }
}