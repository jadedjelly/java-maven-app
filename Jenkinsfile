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
    stages {
        stage('increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }
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
        stage("deploy")
           steps {
                script {
                    echo 'deploying docker image to EC2...'

//                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
//                    def ec2Instance = "ec2-user@18.184.54.160"
//
//                    sshagent(['ec2-server-key']) {
//                        sh "scp server-cmds.sh ${ec2Instance}:/home/ec2-user"
//                        sh "scp docker-compose.yaml ${ec2Instance}:/home/ec2-user"
//                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
//                    }
                }
            }
        }
        stage('commit version update'){
            steps {
                script {
                    echo "Increment version below commented out"
//                    withCredentials([usernamePassword(credentialsId: 'github-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]){
//                        sh 'git remote set-url origin https://$USER:$PASS@github.com/jadedjelly/java-maven-app.git'
//                        sh 'git add .'
//                        sh 'git commit -m "ci: version bump"'
//                        sh 'git push origin HEAD:jenkins-jobs'
//                    }
                }
            }
        }
    }
