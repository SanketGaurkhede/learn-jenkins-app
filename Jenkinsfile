pipeline {
    agent any

    stages {
        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             echo "📦 Starting Build Stage..."
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //         '''
        //     }
        // }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "🧪 Starting Test Stage..."
                    npm test || echo "Tests failed but continuing..."
                '''
            }
        }

      stage('E2E') {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.39.0-noble'
            reuseNode true
        }
    }
    steps {
        sh '''
            echo "🎭 Starting E2E Tests..."
            npm install -g serve
            serve -s build &
            npx playwright test
        '''
    }
}

    }

    post {
        always {
            echo "🧹 Cleaning up and publishing test results..."
            junit allowEmptyResults: true, testResults: 'jest-result/junit.xml'
        }
    }
}
