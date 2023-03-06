pipeline{
        agent any  
        environment{
	    Docker_tag = getDockerTag()
        }
        
        stages{


              stage('Quality Gate Statuc Check'){

               agent {
                docker {
                image 'maven'
                args '-v $HOME/.m2:/root/.m2'
                }
            }
                  steps{
                      script{
                      withSonarQubeEnv('sonar') { 
                      sh "mvn sonar:sonar"
                       }
                      timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
		           sh "mvn clean install"
                  }
               stage('build')
                {
              steps{
                  script{
		           sh 'cp -r ../devops-training@2/target .'
                   sh 'docker build . -t nkarwapanitech/devops-training:$Docker_tag'
		            withCredentials([string(credentialsId: 'docker', variable: 'docker_password')]) {
				    
				        sh 'docker login -u nkarwapanitech -p $docker_password'
				        sh 'docker push nkarwapanitech/devops-training:$Docker_tag'
			        }
                       }
                    }
                 }
                }  
              }



              
	       
	       
        }	       
	      
    
}
