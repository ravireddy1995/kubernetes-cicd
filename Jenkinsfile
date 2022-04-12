pipeline {
    agent any
    
    stages{
	    stage('scm checkout'){
		    steps{
                git branch: 'dev', credentialsId: 'jenkins-bb', url: 'https://prashanth7692@bitbucket.org/albot-tech/albot_hospital_api.git'
            }
	    }
		stage('Gradle Build') {
            steps{
			   sh 'export PATH=$PATH:/opt/gradle/gradle-6.8.2/bin && gradle build'
			   sh 'cp build/libs/hospital-api.jar /var/jenkins_home/workspace/hospital-api/src/Docker/'
			}
	    }
	   
        stage('Build Docker Image'){
            steps{
                sh "cd /var/jenkins_home/workspace/hospital-api/src/Docker && docker rmi -f prashanth7692/common:hosp hosp-java && docker build -t hosp-java ."
                sh "docker tag hosp-java prashanth7692/common:hosp"
            }
        }
        stage('push Image to Dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                sh "docker login -u prashanth7692 -p ${dockerhub}"
                sh "docker push prashanth7692/common:hosp"
               }
            }
        }
        stage('deploy to dev'){
            steps{
                sshagent(['jenkins']) {
                    script{
	                    try{
	                      sh "ssh ubuntu@52.26.51.142 helm upgrade --set image.tag=hosp -n common-apps hospital ./hospitalapi"
	                    }catch(error){
                          sh "ssh ubuntu@52.26.51.142 kubectl get all"
	                    }
                    }
                   
                }
			}
		}
	}
}
