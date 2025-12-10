pipeline {
  agent any

  environment {
    // set env vars here if needed
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    timeout(time: 60, unit: 'MINUTES')
  }

  stages {
    stage('Prepare') {
      steps {
        echo "Checkout source"
        checkout scm
        deleteDir()
      }
    }

    stage('Parallel Work') {
      steps {
        script {
          // Use named parallel branches. Each branch is a closure.
          parallel(
            BuildBranch: {
              // Inside the branch we create a stage to preserve console grouping
              stage('Build') {
                steps {
                  echo "Compiling / Building application..."
                  sh 'echo build-step: simulate build && sleep 3'
                  stash name: 'app-artifact', includes: '**/build/libs/**', allowEmpty: true
                }
                // post within a stage is not allowed under scripted closure, so we handle errors with try/catch if required.
              }
            },
            UnitTestsBranch: {
              stage('Unit Tests') {
                steps {
                  echo "Running unit tests..."
                  sh 'echo running unit tests && sleep 2'
                  // junit '**/build/test-results/test/*.xml'
                }
              }
            },
            LintBranch: {
              stage('Lint / Static Analysis') {
                steps {
                  echo "Running lint/static analysis..."
                  sh 'echo linting && sleep 2'
                }
              }
            },
            IntegrationBranch: {
              stage('Integration Tests') {
                steps {
                  echo "Integration tests (may need artifact)..."
                  // unstash will succeed even if stash was empty because allowEmpty:true was used
                  unstash 'app-artifact'
                  sh 'echo integration tests && sleep 4'
                }
              }
            }
          ) // end parallel
        } // end script
      } // end steps
    } // end Parallel Work

    stage('Post-processing') {
      steps {
        echo "Archive artifacts and publish reports"
        sh 'echo archiving artifacts'
      }
    }
  } // end stages

  post {
    success { echo "Pipeline succeeded" }
    failure { echo "Pipeline failed — send notification (example)" }
    always {
      echo "Cleaning workspace"
      cleanWs()
    }
  }
}
