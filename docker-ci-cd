node{
 stage('Git Checkout'){
	git 'https://github.com/javahometech/my-app'  
 }
 stage('Maven Package'){
	sh 'mvn clean package'
	sh 'mv target/myweb*.war target/myweb.war'
 }
 
 stage('Build Docker Imager'){
   sh 'docker build -t thotasreenu9/myweb:0.0.1 .'
 }
 
 stage('Push to Docker Hub'){
 
	 withCredentials([string(credentialsId: 'github-pwd', variable: 'dockerHubPwd')]) {
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

}
