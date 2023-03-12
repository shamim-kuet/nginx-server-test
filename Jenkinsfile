pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
    }

    stages {
        stage("Verify tooling") {
            steps {
                sh '''
                    docker info
                    docker version
                    docker compose version
                '''
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage("down,build,up containers ") {
            steps {
                script {
                    try {
                        sh ' ssh bmp-server@114.130.89.227 "cd /var/www/container/server11-nginx; \
                             docker-compose down; \
                             docker-compose build ; \
                             docker tag shamimkuet/nginx-server-test shamimkuet/nginx-server-test:${DOCKER_TAG} ; \
                             docker-compose up -d " '                            
                    } catch (Exception e) {
                        echo 'No running container to clear up...'
                    }
                }
            }
        }
        stage("push docker image to docker hub") {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                sh ' docker login -u devopsjrk -p ${dockerhubpwd} '
            }
                sh ' docker push shamimkuet/nginx-server-test:${DOCKER_TAG} '
        }

        }   
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout:true
    return tag
}

