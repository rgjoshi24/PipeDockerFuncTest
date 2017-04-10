pipeline{

	agent none
	  
	environment {
	      MAJOR_VERSION=1
  	}

	stages{

		stage('Unit Test'){
			agent{
				label 'apache'
			}

			steps{
				sh 'ant -f test.xml -v'
				junit 'reports/result.xml'
			}
		}

		stage('Build'){
			agent{
				label 'apache'
			}
			steps{
				sh 'ant -f build.xml -v'
			}
		
			post{
		
			     success{
		    			archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
	     		     }		
			}
		}

		stage('Deploy'){
			agent{
				label 'apache'
			}
			steps{
				sh "cp dist/rectangle_${env.MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/all"
			}
		}
		stage('Functional Test on Centos VM'){
			agent{
				label 'apache'
			}

			steps{

				sh "wget http://rgjosih24-gmail.com4.mylabserver.com/rectangles/all/rectangles_${env.MAJOR_VERISON}.${BUILD_NUMBER}.jar"
				sh "java -jar rectangles_${env.MAJOR_VERSION}.${BUILD_NUMBER}.jar 6 4"
			}
		
		}
		stage('Functional Test on Debian Docker Container'){
			agent{
				docker 'openjdk:8u121-jre'
			}

			steps{

				sh "wget http://rgjosih24-gmail.com4.mylabserver.com/rectangles/all/rectangles_${env.MAJOR_VERISON}.${BUILD_NUMBER}.jar"
				sh "java -jar rectangles_${env.MAJOR_VERSION}.${BUILD_NUMBER}.jar 6 4"
			}
		}
		stage('Promote To Green'){
			agent{
				label 'apache'
			}

			steps{
				sh "cp /var/www/html/rectangles/all/rectangles_${env.MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangles_${env.MAJOR_VERSION}.${BUILD_NUMBER}.jar"
			}
		}
	
}

