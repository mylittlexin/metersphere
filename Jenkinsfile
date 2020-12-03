pipeline {
    agent {
        node {
            label 'master'
        }
    }
    parameters { 
        string(name: 'IMAGE_NAME', defaultValue: 'metersphere', description: '构建后的 Docker 镜像名称')
        string(name: 'IMAGE_FREFIX', defaultValue: 'metersphere', description: '构建后的 Docker 镜像带仓库名的前缀')
        string(name: 'IMAGE_REGISTRY_CRED', defaultValue: 'MS-DOCKER-HUB', description: '需要 push 的 Docker 镜像仓库 credential ID')
    }
    stages {
        stage('Chcekout') {
            steps {
                checkout scm
            }
        }
        stage('Build/Test') {
            steps {
                configFileProvider([configFile(fileId: 'metersphere-maven', targetLocation: 'settings.xml')]) {
                    sh "mvn clean package --settings ./settings.xml"
                }
            }
        }
        stage('Docker build & push') {
            steps {
                withCredentials([usernamePassword(credentialsId: '${IMAGE_REGISTRY_CRED}', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh "docker build --build-arg MS_VERSION=${BRANCH_NAME}-b${BUILD_NUMBER} -t ${IMAGE_NAME}:${BRANCH_NAME} ."
                    sh "docker login -u $user -p $pass ${IMAGE_REGISTRY}"
                    sh "docker tag ${IMAGE_NAME}:${BRANCH_NAME} ${IMAGE_REGISTRY}/${IMAGE_NAME}:${BRANCH_NAME}"
                    sh "docker push ${IMAGE_FREFIX}/${IMAGE_NAME}:${BRANCH_NAME}"
                }
            }
        }
    }
}
