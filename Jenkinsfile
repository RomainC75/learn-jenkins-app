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
        
        // stage('Run Tests'){
        //     parallel {
        //         stage('Unit Test'){
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     // to merge workspaces
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                     #test -f build/index.html
        //                     npm test
        //                 '''
        //             }
        //             post{
        //                 always {
        //                     junit 'jest-results/junit.xml'
        //                 }
        //             }
        //         }
        //         stage('E2E') {
        //             agent {
        //                 docker {
        //                     image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
        //                     // to merge workspaces
        //                     reuseNode true
        //                     // ! NEVER DO THIS ! 
        //                     // args '-u root:root'
        //                 }
        //             }
        //             steps {
        //                 // -> reuse node = the same data
        //                 sh '''
        //                     #// ! if -g : global dependencies need root like rights
        //                     #// ! npm install -g serve
        //                     #// !  serve -s build
                            
        //                     npm install serve
        //                     #// ! in background (&) to avoid blocking the server 
        //                     node_modules/.bin/serve -s build &
        //                     sleep 10
        //                     # // ! warning : content security policy. Ok 
        //                     # // ! Try to run this script in Jenkins Dashboard > Manage Jenkins > section “Tools and actions” > Script Console:
        //                     # // ! read : https://www.jenkins.io/doc/book/security/configuring-content-security-policy/
        //                     # // ! System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "sandbox allow-scripts;")
                            
        //                     npx playwright test --reporter=html
        //                 '''
        //             }
        //             post{
        //                 always {
        //                     publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright Local', reportTitles: '', useWrapperFileDirectly: true])
        //                 }
        //             }
        //         }
        //     }
        // }
        // stage('Production'){
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             // to merge workspaces
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             echo "whooo"
        //             npm install netlify-cli
        //             node_modules/.bin/netlify --version
        //             echo "Deploy to site id : $NETLIFY_SITE_ID"
        //             # node_modules/.bin/netlify status
        //             node_modules/.bin/netlify deploy --dir=build --prod
        //         '''
        //     }
        // }
        // stage('Prod E2E') {

                //     agent {
                //         docker {
                //             image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                //             reuseNode true
                //         }
                //     }
                //     environment {
                //         CI_ENVIRONMENT_URL = 'https://66f2f74656c6a400fb16cf56--jazzy-brioche-b4d566.netlify.app'
                //     }
                //     steps {
                //         sh '''
                //             npx playwright test --reporter=html
                //         '''
                //     }
                //     post{
                //         always {
                //             publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright E2E', reportTitles: '', useWrapperFileDirectly: true])
                //         }
                //     }
                // }
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