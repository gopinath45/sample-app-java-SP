node('linux'){

    def mvnHome = tool 'MAVEN3'

    cleanWs()

    stage ('checkout') {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/gopinath45/sample-app-java-SP.git']]])      
        }
    
    try{
        stage ('Build')  {
        sh "${mvnHome}/bin/mvn -f pom.xml clean install"
        }
    }
    finally{
            jacoco()
    }
    
     stage ('Code Quality scan')  {
       withSonarQubeEnv('Sonarqube') {
        sh "${mvnHome}/bin/mvn -f pom.xml sonar:sonar"
        }
     }
   
     stage("Quality Gate") {
        timeout(time: 1, unit: 'HOURS') {
            waitForQualityGate abortPipeline: true
        }
     } 

     stage ('Upload Artifacts to Nexus') {
         nexusArtifactUploader artifacts: [[artifactId: 'my-app', classifier: '', file: 'target/my-app-2.0-SNAPSHOT.jar', type: 'jar']], credentialsId: 'nexus', groupId: 'com.mycompany.app', nexusUrl: '192.168.29.70:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '2.0-SNAPSHOT'
    
    }
}
