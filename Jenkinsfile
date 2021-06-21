pipeline {
    
    // agent any
    agent { label 'master' }
    environment {
        DOCKER_IMAGE = 'vovxox/nginx-cd'
        DOCKER_ALIAS = 'vovxox/nginx-cd'
        DOCKER_PUSH  = true
    }
    stages {
        stage('checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                timestamps {
                    retry(3) {
                        sh label: 'image build', script: '''
                            export DOCKER_TAG=${BUILD_NUMBER}
                            if [ -z ${DOCKER_TAG##*alpine*} ]; then
                                export DOCKER_FILE="Dockerfile-alpine"
                            else
                                export DOCKER_FILE="Dockerfile"
                            fi
                            docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} --build-arg tag=${DOCKER_TAG} --file ${DOCKER_FILE} --pull ${DOCKER_BUILD_OPS} .
                        '''
                    }
                }
            }
        }
        stage('Push') {
            when {
                environment name: 'DOCKER_PUSH', value: 'true'
            }
            steps {
                timestamps {
                    retry(3) {
                        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_HUB_PASS', usernameVariable: 'DOCKER_HUB_USER')]) {
                            sh label: 'docker login', script: '''
                                docker login -u ${DOCKER_HUB_USER} -p ${DOCKER_HUB_PASS} https://index.docker.io/v1/
                            '''
                        }
                    }
                    retry(3) {
                        sh label: 'image push', script: '''
                            docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                            docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_ALIAS}:${BUILD_NUMBER}
                            docker push ${DOCKER_ALIAS}:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
    }
}

def getEnvName(branchName) {
    if ("int".equals(branchName)) {
        return "int";
    } else if ("production".equals(branchName)) {
        return "prod";
    } else {
        return "dev";
    }
}