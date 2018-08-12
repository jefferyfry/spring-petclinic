
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
        configFileProvider(
          [configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]) {
          sh 'mvn -s $MAVEN_SETTINGS deploy'
        }
      }
    }
  }
}