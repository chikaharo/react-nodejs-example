pipeline {
    agent any
    environment {
        IMAGE_NAME = 'demo-node-app'
        IMAGE_VER = '2.2'
        AWS_REGION = 'ap-southeast-1'
        AWS_URL = "010438465474.dkr.ecr.${AWS_REGION}.amazonaws.com"
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
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_VER} ."
                   
                }
            }
        }
        stage("push-image") {
            steps {
                script {
                     withCredentials([usernamePassword(credentialsId: 'aws-root', usernameVariable: "AWS_ACCESS_KEY_ID", passwordVariable: "AWS_SECRET_ACCESS_KEY")]) {
                        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_URL}"
                        sh "docker tag ${IMAGE_NAME}:${IMAGE_VER} ${AWS_URL}/${IMAGE_NAME}:${IMAGE_VER}"
                        sh "docker push ${AWS_URL}/${IMAGE_NAME}:${IMAGE_VER}"
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
                            def dockerRunCMD = "docker pull ${AWS_URL}/${IMAGE_NAME}:${IMAGE_VER} && docker run -d -p 3080:3080 ${AWS_URL}/${IMAGE_NAME}:${IMAGE_VER}"
                            sh "ssh -o StrictHostKeyChecking=no ec2-user@18.142.237.73 ${dockerRunCMD}"
                        }
                    }
                }
            }
        }
    }
}