pipeline {
    agent any

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
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    // to merge workspaces
                    reuseNode true
                }
            }
            steps {
                sh '''
                    #test -f build/index.html
                    npm test
                '''
            }
        }
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    // to merge workspaces
                    reuseNode true
                    // ! NEVER DO THIS ! 
                    // args '-u root:root'
                }
            }
            steps {
                // -> reuse node = the same data
                sh '''
                    #// ! if -g : global dependencies need root like rights
                    #// ! npm install -g serve
                    #// !  serve -s build
                    
                    npm install serve
                    #// ! in background (&) to avoid blocking the server 
                    node_modules/.bin/serve -s build &
                    sleep 10
                    # // ! warning : content security policy. Ok
                    # // ! Try to run this script in Jenkins Dashboard > Manage Jenkins > section “Tools and actions” > Script Console:
                    # // ! read : https://www.jenkins.io/doc/book/security/configuring-content-security-policy/
                    # // ! System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "sandbox allow-scripts;")
                    
                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post{
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}


// test stage
// watch the build folder . must contain an index.html file

// npm test