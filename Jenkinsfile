pipeline {
  agent any

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
          parallel(
            BuildBranch: {
              stage('Build') {
                steps {
                  echo "Compiling / Building application..."
                  sh 'echo build-step: simulate build && sleep 3'
                  stash name: 'app-artifact', includes: '**/build/libs/**', allowEmpty: true
                }
              }
            },
            UnitTestsBranch: {
              stage('Unit Tests') {
                steps {
                  echo "Running unit tests..."
                  sh 'echo running unit tests && sleep 2'
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
                  echo "Integration tests..."
                  unstash 'app-artifact'
                  sh 'echo integration tests && sleep 4'
                }
              }
            }
          )
        }
      }
    }

    stage('Post-processing') {
      steps {
        echo "Archive artifacts and publish reports"
        sh 'echo archiving artifacts'
      }
    }
  }

  post {
    success { echo "Pipeline succeeded" }
    failure { echo "Pipeline failed" }
    always {
      echo "Cleaning workspace"
      cleanWs()
    }
  }
}

