pipeline {
    agent any

    environment {
        NETLIFY_PROJECT_ID = '26812eb6-c4ac-4faa-9b06-14857c7d4fde'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18'
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

        stage('Test') {
            agent {
                docker {
                    image 'node:18'
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
            agent {
                docker {
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                unstash 'build-artifacts'
                sh '''
                rm -rf .netlify
                npm install netlify-cli
                npx netlify --version

                echo "Linking Netlify project..."
                npx netlify link --site=$NETLIFY_PROJECT_ID

                echo "Deploying production. Project ID: $NETLIFY_PROJECT_ID"
                npx netlify deploy --dir=build --prod --auth=$NETLIFY_AUTH_TOKEN
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

