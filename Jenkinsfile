pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'bbf640fa-8dd2-41f3-9aed-ac34c25323a2'
        NETLIFY_AUTH_TOKEN= credentials('netlify-token')
    
        REGISTRY_NAME = "myciregistry"
        ACR_LOGIN_SERVER = "${REGISTRY_NAME}.azurecr.io"
        REPOSITORY_NAME = "node-express-app"

    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    // to merge workspaces
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    echo "VERSIONS"
                    node --version
                    npm --version
                    
                    # ci : especially designed for ci 
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage ('Build Docker image') {
            steps {
                script {
                 sh "docker build -t ${REGISTRY_NAME}.azurecr.io/${REPOSITORY_NAME}:$BUILD_NUMBER ." 
                }
            }
        }

        stage('Upload Image to ACR') {
            steps{   
                script {
                withCredentials([usernamePassword(credentialsId: 'az acr gmail', usernameVariable: 'SERVICE_PRINCIPAL_ID', passwordVariable: 'SERVICE_PRINCIPAL_PASSWORD')]) {
        sh "docker login ${ACR_LOGIN_SERVER} -u $SERVICE_PRINCIPAL_ID -p $SERVICE_PRINCIPAL_PASSWORD"
                    sh " docker push ${REGISTRY_NAME}.azurecr.io/${REPOSITORY_NAME}:$BUILD_NUMBER"
                    }
                }
            }
      }
        
        
    }    
}


// test stage
// watch the build folder . must contain an index.html file

// npm test


// ! updates 
// document versions before => after

// docker jenkins core update 
// 1 : dcd
//    talke the best image : 
// 2 : docker build -t my-jenkins . 
// 3 : dcu (-d)


// for plugins :
// update in "manage jenkins"