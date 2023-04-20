pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                git url: 'https://github.com/gangaraju587/hardeep.git'    
		            echo "Code Checked-out Successfully!!";
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package'    
		            echo "Maven Package Goal Executed Successfully!";
            }
        }
        
      

	stage('SonarQube analysis') {
            steps {
		// Change this as per your Jenkins Configuration
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn package sonar:sonar'
                }
            }
        }

	stage("Quality gate") {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
	stage('SSH into server') {
            /*when {
                expression {
                    currentBuild.result == 'SUCCESS'
                }
            }*/
            steps {
		    script {
			    
		            sh """
			    whoami
			    ssh-keyscan -t rsa 34.125.22.150
		    	    sudo ssh-keyscan -t rsa 34.125.22.150 >> ~/.ssh/known_hosts
			    sudo ssh -i /home/.ssh/jenkins jenkins@34.125.22.150 << EOF
			    touch test1
		            cd hardeep
			    git pull
			    mvn clean package
			    docker build -t webapp .
			    docker run -d -p 8080:8080 --entrypoint="/bin/sh" webapp -c "sh /usr/local/tomcat/bin/startup.sh;while true; do echo hello; sleep 10;done"
			    EOF
              		    """
                    }
                }
            }
        }
        
    
    post {
        
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
    
    }
}
