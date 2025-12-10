pipeline {
  agent any
  environment {
    // Example: set JAVA_HOME or tool names if required
    // JAVA_HOME = tool name if configured in Jenkins global tool config
  }
  options {
    // Keep only last 10 builds
    buildDiscarder(logRotator(numToKeepStr: '10'))
    // Timeout the whole pipeline after 60 minutes
    timeout(time: 60, unit: 'MINUTES')
  }

  stages {
    stage('Prepare') {
      steps {
        echo "Checkout source"
        checkout scm
        // Clean workspace if you want
        deleteDir()
      }
    }

    stage('Parallel Work') {
      parallel {
        stage('Build') {
          steps {
            echo "Compiling / Building application..."
            // Example build commands (adjust to your build tool)
            // sh './gradlew clean assemble'
            sh 'echo build-step: simulate build && sleep 3'
            // stash the built artifacts for other stages if needed
            stash name: 'app-artifact', includes: '**/build/libs/**', allowEmpty: true
          }
          post {
            failure {
              echo "Build failed — collecting logs"
            }
          }
        }

        stage('Unit Tests') {
          steps {
            echo "Running unit tests..."
            // sh './gradlew test'
            sh 'echo running unit tests && sleep 2'
            // Example of recording results (make sure junit xmls exist)
            // junit '**/build/test-results/test/*.xml'
          }
          post {
            always {
              echo "Unit tests finished"
            }
          }
        }

        stage('Lint / Static Analysis') {
          steps {
            echo "Running lint/static analysis..."
            // sh 'npm run lint' or 'flake8 .' etc.
            sh 'echo linting && sleep 2'
          }
        }

        stage('Integration Tests') {
          steps {
            // If integration needs built artifact, unstash it
            echo "Integration tests (may need artifact)..."
            unstash 'app-artifact' // will be empty if none
            sh 'echo integration tests && sleep 4'
          }
        }
      } // end parallel
    } // end stage Parallel Work

    stage('Post-processing') {
      steps {
        echo "Archive artifacts and publish reports"
        // Example archive (adjust glob)
        // archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
        // junit 'build/test-results/**/*.xml'
        sh 'echo archiving artifacts'
      }
    }
  } // end stages

  post {
    success {
      echo "Pipeline succeeded"
    }
    failure {
      echo "Pipeline failed — send notification (example)"
      // mail to: 'dev-team@example.com', subject: "Build failed", body: "..."
    }
    always {
      echo "Cleaning workspace"
      cleanWs()
    }
  }
}

