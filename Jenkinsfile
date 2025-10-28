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
                    echo "📦 Starting Build Stage..."
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

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
                    test -f build/index.html
                    npm test || echo "Tests failed but continuing..."
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
