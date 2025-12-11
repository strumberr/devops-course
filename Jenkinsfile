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
                TAG=$(uuidgen)
                docker tag myapp:latest ttl.sh/$TAG:2h
                docker push ttl.sh/$TAG:2h
                echo "IMAGE=ttl.sh/$TAG:2h" > image.txt
                '''
            }
        }

        stage('Deploy to Docker VM') {
            steps {
                sh '''
                IMAGE=$(cat image.txt)
                docker rm -f myapp || true
                docker pull $IMAGE
                docker run -d -p 4444:4444 --name myapp $IMAGE
                '''
            }
        }
    }
}
