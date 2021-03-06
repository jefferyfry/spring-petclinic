
pipeline {
  agent { label 'vmware-dynamic' }
  environment {
    VERSION = readMavenPom().getVersion()
  }
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
        sh """
          pkill -f spring-petclinic || true
          mvn org.apache.maven.plugins:maven-dependency-plugin:2.1:get -DrepoUrl=http://192.168.1.14:8081/artifactory/libs-snapshot-local/ -Dartifact=org.springframework.samples:spring-petclinic:${VERSION}:jar
          mvn org.apache.maven.plugins:maven-dependency-plugin:3.0.2:copy -Dartifact=org.springframework.samples:spring-petclinic:${VERSION}:jar -DoutputDirectory=.
        """
        script{
            withEnv(['JENKINS_NODE_COOKIE=dontkill']) {
                sh 'nohup java -jar spring-petclinic-${VERSION}.jar &'
            }
        }
        echo 'Go to http://18.213.161.186:8080/ to see your vSphere VM built petstore clinic!'
      }
    }
  }
}