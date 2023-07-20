pipeline {
  agent any 
  tools {
     maven "MAVEN3"
     jdk "OpenJDK8"
  } 
  
  environment {
     SNAP_REPO = 'vprofile-snapshot'
     NEXUS_USER = 'admin'
     NEXUS_PASS = 'admin@123'
     RELEASE_REPO = 'vprofile-release'
     CENTRAL_REPO = 'vpro-maven-central'
     NEXUSIP = '172.31.19.92'
     NEXUSPORT = '8081'
     NEXUS_GRP_REPO = 'vpro-maven-group'
     NEXUS_LOGIN = 'nexuslogin'
  }  

  stages{
   stage('Build'){
    steps{
      sh 'mvn -s settings.xml -DskipTests install'
        }
      post {
        success {
          echo "Now Archiving"
          archiveArtifacts: '**/*.war'   
          }
       }
     }
  
  stage(test) {
    steps {
  sh 'mvn -s settings.xml latest'
  }
}
  stage(checkstyle-analysis){
    steps {
      sh 'mvn -s settings.xml checkstyle:checkstyle'
      } 
    }
  }
}
