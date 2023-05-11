pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
        timeout(time: 30, unit: 'MINUTES')
    }
	tools {
        go 'go v1.18'
    }
    environment { 
        CC_NAME = sh (script: 'grep CC_NAME /var/lib/jenkins/workspace/landnft-stg/.env | cut -d "=" -f 2', returnStdout: true).trim()
		GO111MODULE = 'on'
		GOPATH = "$HOME/go"
		PATH = "$PATH:$GOROOT/bin:$GOPATH/bin"
    }
    stages {
        stage('SECURITY_CHECK') {
            steps {
				script {
					committerEmail = sh (
      				script: 'git log -1 --pretty=format:"%an"',
      				returnStdout: true
					).trim()
				}
				echo "Committer Email : '${committerEmail}'"
				// slackSend (
				// 	color: 'good',
				// 	channel: '#pl-kalp-build-alerts',
				// 	message: "${env.JOB_NAME} | Job has initiated : #${env.BUILD_NUMBER} by ${committerEmail}")
            	// sh 'go get -v github.com/p2eengineering/kaps'
            	//sh 'go build .'
				sh "go version"
				sh "go install github.com/securego/gosec/v2/cmd/gosec@latest"
                sh "time gosec -fmt=json -out=reports.json ./... || true"
                script {
                   def reports = readJSON(file: 'reports.json')
                    if (reports && reports.issues && reports.issues.size() > 0)  {
                        def message = "Security check failed: ${reports.issues.size()} issues found. Please check the report for more details: ${env.BUILD_URL}artifact/reports.json"
                        // slackSend(color: 'danger', channel: '#pl-kalp-build-alerts', message: message)
                        error message
			exit 1
                    } else {
						// slackSend(color: 'good', channel: '#pl-kalp-build-alerts', message: "No security issues found. Please check the report for more details: ${env.BUILD_URL}artifact/reports.json")
					}
					// def reportContent = readFile('reports.json')
                    // slackSend(color: 'warning', channel: '#pl-kalp-build-alerts', message: 'Security check report:\n```' + reportContent + '```')
            	}
        	}
		}
		stage('STG_BUILD') {
			steps {
				// script {
				// 	committerEmail = sh (
      			// 	script: 'git log -1 --pretty=format:"%an"',
      			// 	returnStdout: true
				// 	).trim()
				// }
				// echo "Committer Email : '${committerEmail}'"
				// slackSend (
				// 	color: 'good',
				// 	channel: '#pl-kalp-build-alerts',
				// 	message: "${env.JOB_NAME} | Job has initiated : #${env.BUILD_NUMBER} by ${committerEmail}")
				echo "Step: BUILD, initiated..."
                sh "whoami"
				echo "${env.BUILD_NUMBER}"
				sh 'pwd'
				// slackSend (
				// 	color: 'warning',
				// 	channel: '#pl-kalp-build-alerts',
				// 	message: "${env.JOB_NAME} | Build is started : #${env.BUILD_NUMBER}")
//				sh "ansible-playbook --extra-vars '@/home/ubuntu/landnft-chaincode-ansible-stg/vars.yml' -e BUILD_NUMBER=${env.BUILD_NUMBER} -e CC_NAME='${env.CC_NAME}' -i /home/ubuntu/landnft-chaincode-ansible-stg/hosts /home/ubuntu/landnft-chaincode-ansible-stg/site.yml"
//				sh "ansible-playbook --extra-vars '@/home/ubuntu/landnft-chaincode-ansible-stg/vars.yml' -e BUILD_NUMBER=${env.BUILD_NUMBER} -e CC_NAME='${env.CC_NAME}' -i /home/ubuntu/landnft-chaincode-ansible-stg/hosts /home/ubuntu/landnft-chaincode-ansible-stg/site2.yml"
			}
		}
		// stage('POST_CHECKS') {
		// 	when{
		// 		branch 'stage'
		// 	}
		// 	steps {
		// 		echo "POST test"
		// 	}	
		// 	post {
		// 		always {
		// 			echo "ALWAYS test1"
		// 		}
		// 		success {
		// 			slackSend (
		// 				color: 'good',
		// 				channel: '#pl-kalp-build-alerts',
		// 				message: "${env.JOB_NAME} | Job has succeeded : #${env.BUILD_NUMBER} in ${currentBuild.durationString.replace(' and counting', '')} \n For more info, please click (<${env.BUILD_URL}|here>)")
		// 		}
		// 		failure {
		// 			slackSend (
		// 				color: 'danger',
		// 				channel: '#pl-kalp-build-alerts',
		// 				message: "${env.JOB_NAME} | @channel - Job has failed #${env.BUILD_NUMBER}\nPlease check full info, (<${env.BUILD_URL}|here>)")
		// 		}
		// 	}
		// }
	}
	// post {
	// 	always {
	// 		echo "ALWAYS post-check"
	// 	}
	// 	aborted {
	// 		slackSend (
	// 			color: '#AEACAC',
	// 			channel: '#pl-kalp-build-alerts',
	// 			message: "${env.JOB_NAME} | Job has aborted : #${env.BUILD_NUMBER} in ${currentBuild.durationString.replace(' and counting', '')} \n For more info, please click (<${env.BUILD_URL}|here>)")
	// 	}
	// 	failure {
	// 		slackSend (
	// 			color: 'danger',
	// 			channel: '#pl-kalp-build-alerts',
	// 			message: "${env.JOB_NAME} | @channel - Job has failed #${env.BUILD_NUMBER}\nPlease check full info, (<${env.BUILD_URL}|here>)")
	// 	}
	// }
}
