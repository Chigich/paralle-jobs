pipeline {
    agent any

    stages {

        stage('Prepare') {
            steps {
                echo "Checking out code..."
                checkout scm
            }
        }

        stage('Build & Test in Parallel') {
            parallel {

                stage('Build') {
                    steps {
                        echo "Building the application..."
                        sh 'echo Simulating build... && sleep 3'
                    }
                }

                stage('Tests') {
                    steps {
                        echo "Running tests..."
                        sh 'echo Simulating tests... && sleep 3'
                    }
                }

            }
        }

        stage('Post Steps') {
            steps {
                echo "Build & Test completed!"
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
    }
}
