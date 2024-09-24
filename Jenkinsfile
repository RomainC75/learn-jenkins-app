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
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    // to merge workspaces
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }
    }
}


// test stage
// watch the build folder . must contain an index.html file

// npm test