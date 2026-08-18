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
                    echo 'Starting HTTP server...'
                    ./node_modules/.bin/serve -s build -l 3000 > /dev/null 2>&1 &
                    SERVER_PID=$!
                    sleep 3
                    
                    echo 'Running Playwright E2E tests...'
                    npx playwright test
                    TEST_RESULT=$?
                    
                    echo 'Stopping server...'
                    kill $SERVER_PID 2>/dev/null || true
                    
                    exit $TEST_RESULT
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
