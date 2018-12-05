pipeline {
  agent none



  options {
  	buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
  }

  stages {
    stage('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build'){
      agent {
        label 'apache'
      }
      steps{
        sh 'ant -f build.xml -v'
      }
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }
    stage('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
    stage("Running on Centos") {
      agent {
        label 'CentOS'
      }
      steps {
        sh "java -jar /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Test on Debian") {
      agent {
        docker 'openjdk:7u181-jre'
      }
      steps {
        sh "java -jar /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage('Promote to Green') {
      steps {
        sh "cp /var/www/html/rectangles/all/ectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }

  }

}
