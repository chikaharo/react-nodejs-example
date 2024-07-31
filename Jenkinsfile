pipeline {
    agent any
    environment {
        
    }
    stages {
        stage("init") {
            steps {
                script {
                    echo "initializing pipeline ..."
                }
            }
        }
        stage("build") {
            steps {
                script {
                    echo "Building app with docker ..."
                    sh "docker build -t demo-node-app:2.0 ."
                   
                }
            }
        }
        stage("push-image") {
            steps {
                script {
                     withCredentials([usernamePassword(credentialsId: 'aws-root', usernameVariable: "AWS_ACCESS_KEY_ID", passwordVariable: "AWS_SECRET_ACCESS_KEY")]) {
                        sh "aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 010438465474.dkr.ecr.ap-southeast-1.amazonaws.com"
                        sh "docker tag demo-node-app:2.0 010438465474.dkr.ecr.ap-southeast-1.amazonaws.com/demo-node-app:2.0"
                        sh "docker push 010438465474.dkr.ecr.ap-southeast-1.amazonaws.com/demo-node-app:2.0"
                    }
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo "Deploy to aws ec2 ..."
                    sshagent(['ec2-server-key']) {
                        withCredentials([usernamePassword(credentialsId: 'aws-root', usernameVariable: "AWS_ACCESS_KEY_ID", passwordVariable: "AWS_SECRET_ACCESS_KEY")]) {
                            def dockerRunCMD = "docker pull 010438465474.dkr.ecr.ap-southeast-1.amazonaws.com/demo-node-app:2.0 && docker run -d -p 3080:3080 010438465474.dkr.ecr.ap-southeast-1.amazonaws.com/demo-node-app:2.0"
                            sh "ssh -o StrictHostKeyChecking=no ec2-user@18.142.237.73 ${dockerRunCMD}"
                        }
                    }
                }
            }
        }
    }
}