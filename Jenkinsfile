pipeline {
    agent any  // Runs on any available agent (you can specify a specific agent if needed)
    
    environment {
        // Define environment variables, such as API keys, credentials, etc.
        NETLIFY_SITE_ID = "70c1198f-4468-4080-a76b-d7c577185a3f"
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')  // Fetch Netlify token from Jenkins credentials store
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'  // Use Docker container with Node.js 18 and Alpine Linux
                    reuseNode true  // Reuse the same node for the container
                }
            }
            steps {
                // Steps to build your project (e.g., installing dependencies, building the app)
                sh 'npm install'  // Install dependencies
                sh 'npm run build'  // Run the build script to build the project
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'  // Use Docker container with Node.js 18 and Alpine Linux
                    reuseNode true
                }
            }
            steps {
                // Steps for running tests
                sh '''
                    test -f build/index.html  // Ensure that the index.html exists in the build folder
                    npm test  // Run tests using npm
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'  // Use Docker container for deployment
                    reuseNode true
                }
            }
            steps {
                // Steps for deploying the application to Netlify
                sh '''
                    npm install netlify-cli  // Install Netlify CLI tool
                    node_modules/.bin/netlify --version  // Check the version of Netlify CLI
                    echo "Deploying to production site"
                    node_modules/.bin/netlify status  // Check Netlify site status
                    node_modules/.bin/netlify deploy --dir=build --prod  // Deploy to production
                '''
            }
        }
    }
}
