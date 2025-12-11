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

        stage('Show Image Location') {
            steps {
                sh "echo 'Pushed image:' && cat image.txt"
            }
        }
    }
}
