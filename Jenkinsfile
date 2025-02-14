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
            steps {
                echo "building the docker image"

                // build the docker image
                sh 'docker build -t amitksunbeam/website .'
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

        stage('remove existing docker service') {
            steps {
                echo "removing existing docker service"

                // remove the service if it already exists
                sh 'docker service rm website'
            }
        }

        stage('create new service') {
            steps {
                echo "creating new service"

                // create a new service
                sh 'docker service create --name website --replicas 2 -p 8090:80 amitksunbeam/website'
            }
        }
    }
}