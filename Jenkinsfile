pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -al
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -al
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo 'Test atage'
                    test -f bulid/index.html || (echo "ERROR: build/index.html not found" && exit 1)
                '''
                
            }
        }
    }
}
