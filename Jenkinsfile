pipeline {
    agent {
        docker {
            //image 'node:18-alpine'
            image 'mcr.microsoft.com/playwright:v1.35.0-focal'
            reuseNode true
        }
    }

    stages {
        stage('Build') {
           
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
                    echo 'Verifying build artifacts...'
                    test -f ./build/index.html || (echo "ERROR: build/index.html not found" && exit 1)
                    echo '✓ build/index.html exists'
                '''
                
                sh '''
                    echo 'Running Jest unit tests...'
                    npm test -- --coverage --watchAll=false
                '''
            }
        }

        stage('E2E') {
            steps {
                sh '''
                    npm install serve
                    serve -s build
                    
                    npx playwright test
                '''
            }   
        }
    }

    post {
        always {
            junit 'test-results/**/*.xml'
        }   
    }
}
