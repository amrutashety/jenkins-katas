pipeline {
  agent any
  stages {
    stage('Clone Down'){
      agent {
            label 'swarm'
          }
      steps{
        stash excludes: '.git', name: 'code'
      }
    }
    stage('Parallel Execution') {
      parallel {
        stage('Hello') {
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
            skipDefaultCheckout(true)
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }
        
        stage('Test app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }

          }
          steps {
            skipDefaultCheckout(true)
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
  post {
    cleanup {
        deleteDir() /* clean up our workspace */
    }
}
}