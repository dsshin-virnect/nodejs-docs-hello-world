/* Nodejs Web Server Docker Based Jenkinsfile */
pipeline {
    agent any

    options {
        timeout(time: 2, unit: 'HOURS')
    }

    environment {
        REPO_URL = sh(returnStdout: true, script: 'git config --get remote.origin.url').trim()
        REPO_NAME = sh(returnStdout: true, script: 'git config --get remote.origin.url | rev | cut -f 1 -d "/" | rev | sed "s/.git//gi";sed "/^ *$/d"').toLowerCase().trim() 
        //PORT = sh(returnStdout: true, script: 'cat docker/Dockerfile | egrep EXPOSE | awk \'{print $2}\'').trim()
        //APP = ' '
        AZURE_SUBSCRIPTION_ID='cac12505-9924-4708-99ff-243a2e777f4b'
        AZURE_TENANT_ID='8895df5f-1e4d-4cb1-8c33-9ec96c34fe15'
        AZURE_STORAGE_ACCOUNT='virnectjenkins'
        AZURE_STORAGE_KEY='vEFF+rCoKxG2MRiYWImtLgU0vWEvipoihgHt/yS+h/DYJKDC+FH2fJnMgF7oVW5zqk/2iLzCaHfz+AStP58qkQ=='
        CONTAINER_REGISTRY='virnectjenkins'
        //RESOURCE_GROUP='resource group'
        //REPO="repo name"
        //IMAGE_NAME="image name"
        //TAG="tag"
    }

    stages {
        /*stage('Clone') {             
            steps {
                echo 'Clone'
                git branch: 'main', credentialsId: 'dsshin', url: 'https://github.com/dsshin-virnect/nodejs-docs-hello-world.git'
            }
         }*/
        
        stage ('build docker image') {
            steps {
                script {
                    sh '''
                    docker build --tag helloworld:$BUILD_NUMBER .
                    docker stop helloworld && docker rm helloworld
                    docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
                    '''
                }
            }
        }
        
        stage('deploy acr') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dsshin_jenkins', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
                            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                            sh 'az account set -s $AZURE_SUBSCRIPTION_ID'
                            //sh 'az acr login --name $CONTAINER_REGISTRY --resource-group $RESOURCE_GROUP'
                            //sh 'az acr build --image $REPO/$IMAGE_NAME:$TAG --registry $CONTAINER_REGISTRY --file Dockerfile . '
                            sh 'az acr login --name $CONTAINER_REGISTRY'
                            //sh 'az acr build --image helloworld:$BUILD_NUMBER --registry $CONTAINER_REGISTRY --file Dockerfile . '
                            sh 'docker pull mcr.microsoft.com/hello-world' 
                        }
            }
        }
        
    }
}
