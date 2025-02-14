pipeline {
    agent any
    stages {
        stage('init environment') {
            steps {
                echo "initializing the environment"
                sh 'export PATH=$PATH:/usr/bin'
            }
        }

        stage('SCM') {
            steps {
                echo "pulling the latest code from the repository"

                // pull the code from github repository
                git branch: 'main', url: 'https://github.com/pythoncpp/website.git'
            }
        }

        stage('build docker image') {
            environment {
                COMMIT_ID = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
            }
            steps {
                echo "building the docker image with commit id: $COMMIT_ID"
                
                // build the docker image
                sh 'docker build -t amitksunbeam/website:$COMMIT_ID .'
            }
        }

        stage('docker login') {
            steps {
                echo "logging into docker hub"

                // login to docker hub
                withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_HUB_TOKEN')]) {
                    sh 'echo $DOCKER_HUB_TOKEN | docker login -u amitksunbeam --password-stdin'
                }
            }
        }

        stage('push docker image') {
            steps {
                echo "pushing the docker image to docker hub"

                // push the docker image to docker hub
                sh 'docker push amitksunbeam/website'
            }
        }

        stage('update deployment file') {
            steps {
                echo "updating the deployment file"

                withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')]) {
                    // update the deployment file
                    sh 'sed -i "s/BUILD_NUMBER/$COMMIT_ID/g" deployment.yaml'

                    git config user.email "pythoncpp@gmail.com"
                    git config user.name "Amit Kulkarni"
                    git add deployment.yaml
                    git commit -m "updated deployment file"
                    git push origin main
                }

                
            }
        }
    }
}