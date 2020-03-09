pipeline{
 agent { label 'myagent'}
 stages{
  stage('Initializing'){
     steps{
     sh """
         docker rmi -f \$(docker images -q) || date
         rm -rf /root/kogito-cloud/
         mkdir -p /root/kogito-cloud
   """
   }
  }
     stage('Clone Sources'){
         steps{
         sh """
         git clone https://github.com/kiegroup/kogito-cloud.git  /root/kogito-cloud
         echo '''schema_version: 1
name: org.kie.kogito.dataindex
version: "0.8.0-rc1"

artifacts:
- name: kogito-data-index-runner.jar
  url: https://repository.jboss.org/org/kie/kogito/data-index-service/8.0.0-SNAPSHOT/data-index-service-8.0.0-20200306.141550-176-runner.jar
  md5: 08c2ab7adf2a58d45864a0abe6de1388

execute:
- script: configure''' >  /root/kogito-cloud/s2i/modules/kogito-data-index/module.yaml

echo '''schema_version: 1
name: org.kie.kogito.jobs.service
version: "0.8.0-rc1"
                    
artifacts:
- name: kogito-jobs-service-runner.jar
  url: https://repository.jboss.org/org/kie/kogito/jobs-service/8.0.0-SNAPSHOT/jobs-service-8.0.0-20200306.141121-100-runner.jar
  md5: 8c3adae62aafd09f078b084db956a4cc                                                               
                                       
execute:
- script: configure''' > /root/kogito-cloud/s2i/modules/kogito-jobs-service/module.yaml

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

 stage('Tagging'){
      steps{
          sh """
             docker tag quay.io/kiegroup/kogito-jobs-service:0.8.0-rc1             quay.io/kaitou786/kogito-jobs-service-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-jobs-service                       quay.io/kaitou786/kogito-jobs-service-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-data-index:0.8.0-rc1               quay.io/kaitou786/kogito-data-index-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-data-index                         quay.io/kaitou786/kogito-data-index-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-springboot-ubi8-s2i:0.8.0-rc1      quay.io/kaitou786/kogito-springboot-ubi8-s2i-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-springboot-ubi8-s2i                quay.io/kaitou786/kogito-springboot-ubi8-s2i-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-springboot-ubi8:0.8.0-rc1          quay.io/kaitou786/kogito-springboot-ubi8-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-springboot-ubi8                    quay.io/kaitou786/kogito-springboot-ubi8-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-quarkus-ubi8-s2i:0.8.0-rc1         quay.io/kaitou786/kogito-quarkus-ubi8-s2i-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-quarkus-ubi8-s2i                   quay.io/kaitou786/kogito-quarkus-ubi8-s2i-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-quarkus-jvm-ubi8:0.8.0-rc1         quay.io/kaitou786/kogito-quarkus-jvm-ubi8-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-quarkus-jvm-ubi8                   quay.io/kaitou786/kogito-quarkus-jvm-ubi8-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-quarkus-ubi8:0.8.0-rc1             quay.io/kaitou786/kogito-quarkus-ubi8-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker tag quay.io/kiegroup/kogito-quarkus-ubi8                       quay.io/kaitou786/kogito-quarkus-ubi8-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
             docker images
             """
       }
  }
  stage('Pushing'){
   steps{
   withDockerRegistry([ credentialsId: "tarun_quay", url: "https://quay.io" ]){
    sh """
    docker push  quay.io/kaitou786/kogito-jobs-service-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-jobs-service-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-data-index-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-data-index-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-springboot-ubi8-s2i-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-springboot-ubi8-s2i-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-springboot-ubi8-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-springboot-ubi8-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-quarkus-ubi8-s2i-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-quarkus-ubi8-s2i-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-quarkus-jvm-ubi8-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-quarkus-jvm-ubi8-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-quarkus-ubi8-nightly:0.8.0-rc1-nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    docker push  quay.io/kaitou786/kogito-quarkus-ubi8-nightly:nightly-\$(echo \${GIT_COMMIT} | cut -c1-7)
    """
   }
   }  
  }
   stage('Finishing'){
       steps{
           sh"""
           rm -rf /root/kogito-cloud
           docker rmi -f \$(docker images -q) || date
           """
       }
   }
 }
}
