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
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.47.2-noble'
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
                    npx playwright test
                '''
            }
        }
    }

    post{
        always {
            junit 'jest-results/junit.xml'
        }
    }
}


// test stage
// watch the build folder . must contain an index.html file

// npm test