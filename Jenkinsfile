pipeline {
    agent any

    tools {
        go "1.24.1"
    }

    stages {
        stage('Test') {
            steps {
                sh "go test ./..."
            }
        }

        stage('Build') {
            steps {
                sh "go build -o main main.go"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build . --tag myapp:latest"
            }
        }

        stage('Push to ttl.sh') {
            steps {
                sh '''
                TAG=$(cat /proc/sys/kernel/random/uuid)
                IMAGE="ttl.sh/$TAG:2h"

                docker tag myapp:latest $IMAGE
                docker push $IMAGE

                echo $IMAGE > image.txt
                '''
            }
        }

        stage('Deploy to Docker VM') {
            steps {
                sshagent(['sshkey']) {
                    sh '''
                    IMAGE=$(cat image.txt)

                    # Remove old container on Docker VM
                    ssh -o StrictHostKeyChecking=no laborant@docker "docker rm -f myapp || true"

                    # Pull fresh image
                    ssh -o StrictHostKeyChecking=no laborant@docker "docker pull $IMAGE"

                    # Run container on port 4444
                    ssh -o StrictHostKeyChecking=no laborant@docker "docker run -d -p 4444:4444 --name myapp $IMAGE"
                    '''
                }
            }
        }


    }
}
