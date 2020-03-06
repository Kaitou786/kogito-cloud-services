pipeline{
 agent { label 'myagent'}
 stages{
     stage('Clone Sources'){
         steps{
         sh """
         mkdir -p /root/kogito-cloud
         git clone https://github.com/kiegroup/kogito-cloud.git  /root/kogito-cloud
         """
        }
    }
   stage('Build'){
       steps{
           withDockerRegistry([ credentialsId: "tarkhand-rregistry", url: "https://registry.redhat.io" ]){
               sh """ 
               export MAVEN_MIRROR_URL=http://nexus3-kogito-tools.apps.kogito.automation.rhmw.io/repository/maven-public/
               cd /root/kogito-cloud/s2i && make build
               """
           }
       }
   }
   stage('Test'){
       steps{
           sh """
           export MAVEN_MIRROR_URL=http://nexus3-kogito-tools.apps.kogito.automation.rhmw.io/repository/maven-public/
           cd /root/kogito-cloud/s2i && make test
           """
       }
   }
 stage('Pushing'){
      stepes{
          sh """
             docker images
             """
       }


  }
   stage('Finishing'){
       steps{
           sh"""
           rm -rf /root/kogito-cloud
           """
       }
   }
 }
}
