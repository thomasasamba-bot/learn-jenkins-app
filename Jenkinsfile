pipeline {
    agent any

    environment {
        NETLIFY_PROJECT_ID = '26812eb6-c4ac-4faa-9b06-14857c7d4fde'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent{
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                ls -la
                node -v
                npm -v
                npm ci
                npm run build
                ls -la
                '''
            }
        }

        stage( 'Test'){
            agent{
                docker {
                    image 'node:18-alpine'
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

        stage('Deploy') {
            agent{
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install netlify-cli
                node_modules/.bin/netlify --version
                echo "Deploying production. Project ID: $NETLIFY_PROJECT_ID"
                node_modules/.bin/netlify --status
                '''
            }
        }

    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
        success {
            echo 'Build and tests succeeded!'
        }
        failure {
            echo 'Build or tests failed.'
        }
    }
}
