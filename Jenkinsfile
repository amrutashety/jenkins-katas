pipeline {
  agent any
  stages {
    stage('Hello') {
      parallel {
        stage('Parallel Execution') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }

          }
          steps {
            sh 'sh "ci/build-app.sh"'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

  }
}