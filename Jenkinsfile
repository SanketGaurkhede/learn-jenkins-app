pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '6699f721-92eb-46be-ba9d-068340839520'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token') // ✅ fixed syntax
    }

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
                    echo "🧱 Building project..."
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la build
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "🧪 Running unit tests..."
                            npm test
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            echo "🎭 Running Playwright E2E tests..."
                            npm install serve
                            npx serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([
                                reportDir: 'playwright-report',
                                reportFiles: 'index.html',
                                reportName: 'Playwright HTML Report'
                            ])
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "🚀 Installing Netlify CLI..."
                    npm install -g netlify-cli

                    echo "Netlify version:"
                    netlify --version

                    echo "Deploying to Netlify site: $NETLIFY_SITE_ID"
                    netlify status --auth $NETLIFY_AUTH_TOKEN
                    netlify deploy --dir=build --prod --site $NETLIFY_SITE_ID --auth $NETLIFY_AUTH_TOKEN
                '''
            }
        }
    }
}
