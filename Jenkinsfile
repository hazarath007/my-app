node{
 stage('Git Checkout'){
	git 'https://github.com/javahometech/my-app'  
 }
 stage('Maven Package'){
	 def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/mvn clean package"
	sh 'mv target/myweb*.war target/myweb.war'
 }
 
 stage('Build Docker Imager'){
   sh 'docker build -t thotasreenu9/myweb:0.0.1 .'
 }
 
 stage('Push to Docker Hub'){
 
	 withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerHubPwd')]) {

        sh "docker login -u thotasreenu9 -p ${dockerHubPwd}"
     }
	 sh 'docker push thotasreenu9/myweb:0.0.1'
 }
 stage('Remove Previous Container'){
	try{
		def dockerRm = 'docker rm -f myweb'
		sshagent(['docker-dev']) {
			sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.41.29 ${dockerRm}"
		}
	}catch(error){
		//  do nothing if there is an exception
	}
 }
 stage('Deploy to Dev Environment'){
   def dockerRun = 'docker run -d -p 8080:8080 --name myweb thotasreenu9/myweb:0.0.1 '
   sshagent(['docker-dev']) {
    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.41.29 ${dockerRun}"
   }

 }
stage('E-mail naotification'){
         mail bcc: '', body: '''job buils status
         thanks 
         srinu''', cc: '', from: '', replyTo: '', subject: 'jenkins ci/cd job', to: 'sreenu0377@gmail.com'
     }
 stage('slack notifications'){
         slackSend baseUrl: 'https://hooks.slack.com/services/', 
         channel: '#jenkins-pipeline-demo', 
         color: 'good',
         message: 'welcome jenkins pipeline job', 
         teamDomain: 'jenkins-pipeline-demo',
         tokenCredentialId: 'slack-demo-1'
     }
}
