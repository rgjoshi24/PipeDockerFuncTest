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
				sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
				sh "cp dist/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
			}
		}
		stage('Functional Test on Centos VM'){
			agent{
				label 'apache'
			}

			steps{

				sh "wget http://rgjosih24-gmail-com4.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar 6 4"
			}
		
		}
		stage('Functional Test on Debian Docker Container'){
			agent{
				docker 'openjdk:8u121-jre'
			}

			steps{

				sh "wget http://rgjosih24-gmail-com4.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar 6 4"
			}
		}
		stage('Promote To Green'){
			agent{
				label 'apache'
			}

			when{
				branch 'development'
			}

			steps{
				sh "cp /var/www/html/rectangles/all/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${MAJOR_VERSION}.${BUILD_NUMBER}.jar"
			}
		}
		stage('Promote Dev Branch to Master'){
			agent{
				label 'apache'
			}
			when{

				branch 'development'
			}

			steps{
				echo "Stashing any local changes"
				sh 'git stash'
				echo "Checking out Dev Branch"
				sh 'git checkout development'
				ehco "Checking out Master Branch"
				sh 'git checkout master'
				echo "Merging from Dev to Master"
				sh 'git merge devlopment'
				echo "Pushing to Master"
				sh 'git push origin master'
			}
		}
	
	}
}

