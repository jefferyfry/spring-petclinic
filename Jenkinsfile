
pipeline {
  agent { label 'vmware-dynamic' }
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install'
      }
    }
    stage('Deploy to Repo') {
      steps {
        configFileProvider(
          [configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]) {
          sh 'mvn -s $MAVEN_SETTINGS deploy'
        }
      }
    }
    stage('Launch on Staging Server') {
      agent { label 'staging-server' }
      steps {
        script {
            def pom = readMavenPom file: 'pom.xml'
            VERSION = pom.version
        }
        configFileProvider(
          [configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]) {
          sh 'mvn -s $MAVEN_SETTINGS deploy'
          sh 'mvn org.apache.maven.plugins:maven-dependency-plugin:2.1:get -DrepoUrl=http://192.168.1.14:8081/artifactory/libs-snapshot-local/ -Dartifact=org.springframework.samples:spring-petclinic:${VERSION}:jar'
          sh 'mvn org.apache.maven.plugins:maven-dependency-plugin:3.0.2:copy -Dartifact=org.springframework.samples:spring-petclinic:${VERSION}:jar -DoutputDirectory=. && java -jar spring-petclinic-${VERSION}.jar'
        }
      }
    }
  }
}