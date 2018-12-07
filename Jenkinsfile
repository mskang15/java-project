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
        sh 'mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}'
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }
    stage("Running on Centos") {
      agent {
        label 'CentOS'
      }
      steps {
        sh "java -jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage('Promote to Green') {
      agent {
        label 'apache'
      }
      when {
        branch 'master'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NAME}.jar /var/www/html/rectangles/green/rectangle_${env.BRANCH_NUMBER}.jar"
      }
    }
    stage('Promote Development Branch to Master') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        echo "Stashing any local changes"
        sh 'git stash'
        echo "Chekcing out development branch"
        sh 'git checkout development'
        echo "Checking out master branch"
        sh 'git checkout master'
        echo 'Merging development into master branch'
        sh 'git push origin master'
      }
    }

  }

}
