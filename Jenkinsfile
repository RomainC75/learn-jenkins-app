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
                }
            }
            steps {
                // -> reuse node = the same data
                sh '''
                    npm install -g serve
                    serve -s build
                    npx playwright test
                '''
            }
        }
    }

    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}


// test stage
// watch the build folder . must contain an index.html file

// npm test