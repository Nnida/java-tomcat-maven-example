node{
      
      stage('SCM Checkout'){
         git 'https://github.com/albertlovescloud/java-tomcat-maven-example'
      }
  
      stage('Mvn Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package"
      }
      
     stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }  
      
    stage('Build Docker Image'){
         sh 'docker build -t lovescloud/javatomcatreliancedemo:2.0.0 .'
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwdalbert', variable: 'dockerPWDalbert')]) {
              sh "docker login -u lovescloud -p ${dockerPWDalbert}"
         }
        sh 'docker push lovescloud/javatomcatreliancedemo:2.0.0'
      }

   stage('Stop running containers'){        
         def listContainer='sudo docker ps'
         def scriptRunner='sudo ./stopscript.sh'
         def stopContainer='sudo docker stop $(docker ps -a -q)'
         sshagent(['dockerdeployserver2']) {
              sh "ssh -o StrictHostKeyChecking=no ubuntu@35.168.86.36 ${scriptRunner}"            
         }
    } 
   stage('Pull Docker Image and Deploy'){        
         
            def dockerContainerName = 'javatomcatsampledemo-$JOB_NAME-$BUILD_NUMBER'
            def dockerRun= "sudo docker run -p 8080:8080 -d --name ${dockerContainerName} lovescloud/javatomcatreliancedemo:2.0.0"         
            sshagent(['dockerdeployserver2']) {
              sh "ssh -o StrictHostKeyChecking=no ubuntu@35.168.86.36 ${dockerRun}"              
         }
   }
 }


