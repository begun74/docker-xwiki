pipeline {  

	agent any
		environment {    
			versionXwiki = "10"
			registryXwiki = "begun74/docker-xwiki:v$versionXwiki"        
			registryCredential = 'dockerhub'        
			DB_HOST = "mysql-xwiki"    
			DB_USER = "xwiki"    
			DB_PASS = "xwiki"    
			DB_NAME = "xwiki"  

			API_HEADER='bvv:1173d2420bbf23a37152496789cc26ef27'

		}  

		stages {    
			stage('Cloning Git docker-xwiki') {      
				steps {         
					sh "rm -rf *"        
					git branch: 'sa-project' , url: 'https://github.com/begun74/docker-xwiki.git'
					//git 'git@github.com:xwiki-contrib/docker-xwiki.git'      
				}    
			}    
			stage('Building image docker-xwiki') {      
				steps{        
					script {            
						//sh 'ls -la'            
						sh "cp  $versionXwiki/mysql-tomcat/Dockerfile ./"            
						sh "cp -pr $versionXwiki/mysql-tomcat/tomcat ./"            
						sh "cp -pr $versionXwiki/mysql-tomcat/xwiki ./"                        
			
						//Rename DB parameters in hibernate.cfg.xml for connecting to Mysql 5.7            
						sh "sed -i 's/replacecontainer/$DB_HOST/g' xwiki/hibernate.cfg.xml"            
						sh "sed -i 's/replaceuser/$DB_USER/g' xwiki/hibernate.cfg.xml"            
						sh "sed -i 's/replacepassword/$DB_PASS/g' xwiki/hibernate.cfg.xml"            
						sh "sed -i 's/replacedatabase/$DB_NAME/g' xwiki/hibernate.cfg.xml"                    

						dockerImage = docker.build registryXwiki        
					}      
				}    
			}     
			stage('Deploy Image  docker-xwiki') {      
				steps{        
					script {          
						docker.withRegistry( '', registryCredential ) {            
							dockerImage.push()          
						}        
					}      
				}    
			}    
			stage('Remove Unused docker image  docker-xwiki') {      
				steps{                
					sh "docker rmi $registryXwiki"        
					//sh "docker images"      
				}    
			}  
		}      
		post {            
			success {                
				//slackSend (channel: '#jenkins_news',color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [$docker-xwiki $versionXwiki]' (${env.BUILD_URL})")            

				  //sh """   curl -X POST -u $API_HEADER http://jenkins.local/view/SA/job/02.Docker-xwiki.sa-project/build """
			}            
			failure {                
				//slackSend (channel: '#jenkins_news',color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [$docker-xwiki $versionXwiki]' (${env.BUILD_URL})")            
			}        
		}
}
