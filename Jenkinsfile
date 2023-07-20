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
     SONARSERVER = 'sonarserver'
     SONARSCANNER = 'sonarscanner'
  }  

  stages{
   stage('Build'){
    steps{
      sh 'mvn -s settings.xml -DskipTests install'
        }
      post {
        success {
          echo "Now Archiving."
          archiveArtifacts artifacts: '**/*.war'   
          }
       }
     }
  
  stage(test) {
    steps {
      sh 'mvn -s settings.xml test'
  }
}
  stage(checkstyle){
    steps {
      sh 'mvn -s settings.xml checkstyle:checkstyle'
      } 
    }
  stage('SonarQube analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
                withSonarQubeEnv("${SONARSERVER}") {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=my-project \
                        -Dsonar.sources=src/main/java \
                        -Dsonar.java.binaries=target/classes \
                        -Dsonar.tests=src/test/java \
                        -Dsonar.test.inclusions=**/*Test.java \
                        -Dsonar.junit.reportPaths=target/surefire-reports \
                        -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml"
                }
            }
        }
  stage(qualitygate){
    steps {
      timeout(time: 1,unit: 'HOURS')
         waitForQualityGate abortPipeline: true
        }
      } 
    }  
  } 

        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                  groupId: 'QA',
                  version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                  repository: "${RELEASE_REPO}",
                  credentialsId: "${NEXUS_LOGIN}",
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }
    }
    
 post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
