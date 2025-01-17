pipeline {
  agent any
  environment { 
        docker_username = 'amrutashety'
    }
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
          options {
            skipDefaultCheckout true
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash excludes: '.git', name: 'code'
          }
        }

        stage('Test app') {
          agent {
            docker {
              image 'gradle:6-jdk11'
            }
          }
          options {
            skipDefaultCheckout true
          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

    stage('Push Docker App'){
      agent {
            label 'swarm'
          }
      when { 
        beforeAgent true 
        branch "master"
      }
      environment {
        DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
      }
      
      steps {
        unstash 'code' //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'
      }
    }
    
    stage('Component testing'){
      agent {
            label 'swarm'
          }
      when { 
        beforeAgent true 
        not { branch 'dev/*' }
      }
      steps {
        unstash 'code' //unstash the repository code
        sh 'ci/component-test.sh'
      }
    }
  }
  post {
    cleanup {
        deleteDir() /* clean up our workspace */
    }
  }
}