pipeline {
    agent any
    tools {
        maven 'Maven' 
	  
	    
    }
   stages {
   	stage('SonarQube Analysis'){
        	steps {
                	withSonarQubeEnv('Sonarqube') {
                    		sh "mvn -f acme-salesforce-accounts-sapi/pom.xml sonar:sonar -Dsonar.sources=src/"
                    		script {
		    			LAST_STARTED = env.STAGE_NAME
                    			timeout(time: 1, unit: 'HOURS') { 
                        			sh "curl -u admin:admin -X GET -H 'Accept: application/json' http://104.248.169.167:9000/api/qualitygates/project_status?projectKey=com.mycompany:acme-salesforce-accounts-sapi > status.json"
                        			def json = readJSON file:'status.json'
                        			echo "${json.projectStatus}"
                        			if ("${json.projectStatus.status}" == "ERROR") {
                            				currentBuild.result = 'FAILURE'
                           				error('Pipeline aborted due to quality gate failure.')
                           			}
                        		}     
                    		}
                	}
                }
	}
       
  
      stage('Build') {
      		steps {
	    		script {
				configFileProvider([configFile(fileId: '706c4f0b-71dc-46f3-9542-b959e2d26ce7', variable: 'settings')]){
				LAST_STARTED = env.STAGE_NAME
				sh "mvn -f acme-salesforce-accounts-sapi/pom.xml -s $settings clean install  -DskipTests"     
				}	
		    	} 
            	}    
      } 
	   
     /* stage('Build image') {
      		steps {
        		script {
			//          sh "docker stop acme-salesforce-accounts-api" 
        		//   	sh "docker rm acme-salesforce-accounts-sapi"
			   	LAST_STARTED = env.STAGE_NAME
			   	sh "docker build -t acme-salesforce-accounts-sapi:mule -f Dockerfile ."
                	 
                        }
               }
      }

      stage('Run container') {
      		steps {
        		script {
			     	LAST_STARTED = env.STAGE_NAME
          		    	sh ' docker run -itd -p 8082:8081 --name acme-salesforce-accounts-sapi acme-salesforce-accounts-sapi:mule'
				sh 'sleep 60'
       			}
		}
     }*/
   	
     stage ('Munit Test'){
        	steps {
			script {
				//configFileProvider([configFile(fileId: '706c4f0b-71dc-46f3-9542-b959e2d26ce7', variable: 'settings')]){
			   	LAST_STARTED = env.STAGE_NAME
			   	sh "mvn -f acme-salesforce-accounts-sapi/pom.xml -Dhttp.port=8086 -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true -Dinternal.repo.username=admin -Dinternal.repo.password=NjcNexus@123 -Dmaven.repo.remote=http://104.248.169.167:8081/repository/maven-dependency-releases/ test"
                           
				//}	
			}		
        	}    
     }
   /* stage('Functional Testing'){
        	steps {
			script {
				LAST_STARTED = env.STAGE_NAME
				sh "mvn -f cucumber-API-Framework/pom.xml test -Dtestfile=cucumber-API-Framework/src/test/javarunner.TestRunner.java"
				cucumber(failedFeaturesNumber: -1, failedScenariosNumber: -1, failedStepsNumber: -1, fileIncludePattern: 'cucumber.json', jsonReportDirectory: 'cucumber-API-Framework/target', pendingStepsNumber: -1, skippedStepsNumber: -1, sortingMethod: 'ALPHABETICAL', undefinedStepsNumber: -1)
			}
        		
             	}   
     }*/
	
	   
    stage('Archetype'){
        	steps {
			script {
		    		LAST_STARTED = env.STAGE_NAME
		    		configFileProvider([configFile(fileId: '706c4f0b-71dc-46f3-9542-b959e2d26ce7', variable: 'settings')]){
                    			sh "mvn -f acme-salesforce-accounts-sapi/pom.xml -s $settings archetype:create-from-project"
		    			sh "mvn -f acme-salesforce-accounts-sapi/target/generated-sources/archetype/pom.xml -s $settings clean deploy -DaltSnapshotDeploymentRepository=nexus-snapshots::http://104.248.169.167:8081/repository/maven-snapshots/"
                  		} 
			}
              }   
     }
	  
    /*stage('Deploy to Cloudhub'){
        	steps {
			script {
				configFileProvider([configFile(fileId: '706c4f0b-71dc-46f3-9542-b959e2d26ce7', variable: 'settings')]){
				LAST_STARTED = env.STAGE_NAME
				sh 'mvn -f acme-salesforce-accounts-sapi/pom.xml package deploy -DmuleDeploy -DskipTests -Danypoint.username=njcdemo -Danypoint.password=Njclabs@123 -DapplicationName=acme-salesforce-accounts-api -Dcloudhub.region=us-east-2 -Danypoint.platform.client_id=5247b795e808483ab96a094322027054 -Danypoint.platform.client_secret=f4B43f0c375C4DC797A471065DA234Af' 
				}
			}
             	}
    }
	   
    stage('Email') {
      		steps {
			script {
          		    	readProps= readProperties file: 'cucumber-API-Framework/email.properties'
          		    	echo "${readProps['email.to']}"
        		    	emailext(subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', body: 'Build is Success.Please find the functional testing reports. In order to check the logs, please go to url: $BUILD_URL'+readFile("apiops-anypoint-jenkins-sapi/emailTemplate.html"), attachmentsPattern: 'cucumber-API-Framework/target/cucumber-reports/report.html', from: "${readProps['email.from']}", mimeType: "${readProps['email.mimeType']}", to: "${readProps['email.to']}")
                        }
		}
    }   

    stage('Kill container') {
      		steps {
        		script {
	  	        	LAST_STARTED = env.STAGE_NAME		
          		    	sh 'docker rm -f acme-salesforce-accounts-sapi'
        		}
      		}
    	}
   }
   post {
        failure {
	    script {
	    		emailbody = "Build Failed at $LAST_STARTED Stage. Please find the attached logs for more details."
          		readProps= readProperties file: 'cucumber-API-Framework/email.properties'
				emailext(subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', body: "$emailbody", attachLog: true, from: "${readProps['email.from']}", to: "${readProps['email.to']}")
                    }
            
        }
    }*/
   }}
