
pipeline {
  agent { label 'vmware-dynamic' }
  stages {
    stage('Maven Build') {
      steps {
        sh 'mvn clean package'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }
    stage('Push to Repo') {
      steps {
        sh 'mvn deploy'
      }
    }
    /*stage('Deploy to Prod Server') {
      agent { label 'vmware-prod' }
      steps {
        
      }
    }*/
  }
}