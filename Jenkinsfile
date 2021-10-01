import groovy.transform.Field

podTemplate(label: 'bc16', containers: [
	containerTemplate(name: 'docker', image: 'docker:19.03', command: 'cat', ttyEnabled: true)],
	volumes: [hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')]
) 
{
    node('bc16'){


        environment {
        //VERSION = "${env.GIT_COMMIT}"
        DOCKERHUB_CREDENTIALS= credentials('dockerhub_token_sss')
        //MY_KUBECONFIG = credentials('config-file')
    }
    withEnv([
        "VERSION=${env.GIT_COMMIT}",


    ]){
    stage('Checkout Source') {
     
        git 'https://github.com/sharan-sripada/bc16-frontend.git'
      
    }
	    
	    stage('Build Docker'){
         
          
            container('docker'){

            sh 'docker build -t sharansripada/fe_jenkins:${VERSION} .'
            sh 'docker images'
            
        }
	    }
	    
	    stage('Push Docker'){
	        
	            container('docker'){
	            
	            
	            
	            withCredentials([usernamePassword(credentialsId: 'dockerhub_token_sss', usernameVariable: 'username', passwordVariable: 'password')]) {
  sh 'echo $PASSWORD'
               sh 'docker login -u $username -p $password'
  echo USERNAME

  echo "username is $USERNAME"

	            //sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
	            sh 'docker push sharansripada/fe_jenkins:${VERSION}'
	            }
	           
	        
	        }
	    }

         stage ('Invoking helm build') {
        	
		    build job: 'helm-bc16', parameters: [string(name: 'version', value: env.GIT_COMMIT)]
	    }








    }
    }

}