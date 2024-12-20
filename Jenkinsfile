pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'sample-app'
        DOCKER_REGISTRY = 'deenesh7/sample-app'
        CLOUD_BUCKET = 's3://deenesh7-bucket'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'git-credentials', url: 'https://github.com/Deenesh7/python.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'docker run --rm $DOCKER_IMAGE pytest tests/'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-hub-credentials']) {
                        sh 'docker tag $DOCKER_IMAGE $DOCKER_REGISTRY:latest'
                        sh 'docker push $DOCKER_REGISTRY:latest'
                    }
                }
            }
        }

        stage('Simulate Cloud Deployment') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                        sh 'aws s3 cp deployment.zip $CLOUD_BUCKET'
                    }
                }
            }
        }
    }
}
