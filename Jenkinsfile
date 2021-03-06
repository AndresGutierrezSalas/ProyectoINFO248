pipeline {
  agent none 
  stages {
    stage('Checkout, Test & Build') {
        agent {
          docker {
            image 'node' 
            args '-p 3001:3000'
          }
        }
        environment {
          HOME = '.'
        }
        stages {
          stage('Install') {
            steps {
              sh 'npm install'
              sh 'npm install primeng --save'
              sh 'npm install primeicons --save'
              sh 'npm install primeflex --save'
            }
          }
          stage('Test') {
            steps {
              sh './jenkins/scripts/test.sh'
            }
          }
          stage('Build') {
            steps {
              sh './jenkins/scripts/build.sh'
            }
          }
          stage('Archive') {
            steps {
              archiveArtifacts 'build/**'
            }
          }
        }
    }
    stage('Deploy') {
      agent {
        label 'master'
      }
      options {
        skipDefaultCheckout()
      }
      steps {
        sh 'rm -rf /var/www/neuss'
        sh 'mkdir /var/www/neuss'
        sh 'cp -Rp build/** /var/www/neuss'
        sh 'docker stop neuss || true && docker rm neuss || true'
        sh 'docker run -dit --name neuss -p 8002:80 -v /var/www/neuss/:/usr/local/apache2/htdocs/ httpd:2.4'
      }
    }
  }
}