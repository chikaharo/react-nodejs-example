pipeline {
    agent any
    
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
                }
            }
        }
        stage("deploy") {
            script {
                steps {
                    echo "Deploy to aws ec2 ..."
                    sshagent(['ec2-server-key']) {
                        def dockerRunCMD = "docker run -d -p 3080:3080 dinhuy975/demo-node-app:1.2"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@18.142.237.73 ${dockerRunCMD}"
                    }
                }
            }
        }
    }
}