pipeline {
    agent any
    environment {
      
	    GIT_URL = "git@github.ibm.com:sba/registry.git"
		DOCKER_REPO="registry.cn-beijing.aliyuncs.com/damondocker/eurake"
		DOCKER_REG="https://registry.cn-beijing.aliyuncs.com"
		dockerImage = ''
      
    }
    stages {
    
    	stage('CheckOut Code'){
         	steps{
            	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: GIT_URL]]])
            	}
              }
        stage('Maven Build'){
        	steps{
        	    sh 'mvn clean install -DskipTests'
        	}

        }
        
        stage('Building image') {
	      steps{
	        script {
	           docker.withRegistry( DOCKER_REG) {dockerImage = docker.build DOCKER_REPO + ":$BUILD_NUMBER"
	           }
	        }
	      }
	    }
	    stage('Push Image') {
      steps{
        script {
		   docker.withRegistry( DOCKER_REG ) {
		            dockerImage.push()
		          }
		        }
		      }
		}
		
		stage('Deploy Image to K8s') {
      steps{
        script {
        	sh "sed -i 's/{version}/" + BUILD_NUMBER + "/g' deployment.yaml"
	   		sh 'kubectl apply -f deployment.yaml'
		      }
		}
		}
		
		
		stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $DOCKER_REPO:$BUILD_NUMBER"
      }
    }
   }
  

}