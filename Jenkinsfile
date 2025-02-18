pipeline {
    agent any
    stages {
        
        stage('init environment') {
            steps {
                echo "initializing the environment"
                sh 'export PATH=$PATH:/usr/bin:/usr/local/bin'
            }
        }

        stage('build docker image') {
            
            steps {
                echo "building the docker image with commit id: $BUILD_NUMBER"
                
                // build the docker image
                sh '/usr/local/bin/docker build -t amitksunbeam/website:$BUILD_NUMBER .'
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
                sh '/usr/local/bin/docker push amitksunbeam/website:$BUILD_NUMBER'
            }
        }

        stage('update deployment file') {
            steps {
                echo "updating the deployment file : ${BUILD_NUMBER}"

                withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')]) {
                    // update the deployment file
                    sh 'sed -i "s/website:.*/website:$BUILD_NUMBER/g" deployment.yaml'

                    sh 'git config user.email "pythoncpp@gmail.com"'
                    sh 'git config user.name "Amit Kulkarni"'
                    sh 'git add deployment.yaml'
                    sh 'git commit -m "updated deployment file"'
                    sh 'git push https://${GITHUB_TOKEN}@github.com/pythoncpp/test-app HEAD:main'
                }

                
            }
        }
    }
}