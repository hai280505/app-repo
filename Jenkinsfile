pipeline {
    agent any

    environment {
        REGISTRY = '192.168.56.104'
        FE_IMAGE = "${REGISTRY}/demo/repairshop-frontend"
        FE_TAG   = "5.0.${BUILD_NUMBER}"
    }

    stages {
        stage('Check Docker') {
            steps { sh 'docker version' }
        }

        stage('Build frontend') {
            steps {
                dir('repair-shop-frontend') {
                    sh 'docker build -t $FE_IMAGE:$FE_TAG .'
                }
            }
        }

        stage('Push frontend to Harbor') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'harbor-cred',
                                                  usernameVariable: 'REG_USER',
                                                  passwordVariable: 'REG_PASS')]) {
                    sh 'echo "$REG_PASS" | docker login $REGISTRY -u "$REG_USER" --password-stdin'
                    sh 'docker push $FE_IMAGE:$FE_TAG'
                }
            }
        }
    }

    post {
        always { sh 'docker logout $REGISTRY' }
    }
}
