pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                    sshPublisher(
	                    failOnError: true,
			    continueOnError: false,
	                    publishers: [
		                    sshPublisherDesc(
			                    configName: 'staging_server',
			                    sshCredentials: [
                       	            username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
			                    transfers: [
				                    sshTransfer(
					                    excludes: '', 
					                    execTimeout: 120000,
					                    flatten: false,
					                    makeEmptyDirs: false,
					                    noDefaultExcludes: false,
					                    patternSeparator: '[, ]+',
					                    remoteDirectory: '/home/deploy/',
					                    remoteDirectorySDF: false,
					                    removePrefix: 'dist/',
					                    sourceFiles: 'dist/trainSchedule.zip',
							    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /home/deploy/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-schedule'
						    )],
			                            usePromotionTimestamp: false,
			                            useWorkspaceInPromotion: false,
			                            verbose: true
				    		    )
			                    ] )
                }
	    }
    }
    stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                    sshPublisher(
	                    failOnError: true,
			    continueOnError: false,
	                    publishers: [
		                    sshPublisherDesc(
			                    configName: 'staging_server',
			                    sshCredentials: [
                       	            username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
			                    transfers: [
				                    sshTransfer(
					                    excludes: '', 
					                    execTimeout: 120000,
					                    flatten: false,
					                    makeEmptyDirs: false,
					                    noDefaultExcludes: false,
					                    patternSeparator: '[, ]+',
					                    remoteDirectory: '/home/deploy/',
					                    remoteDirectorySDF: false,
					                    removePrefix: 'dist/',
					                    sourceFiles: 'dist/trainSchedule.zip',
							    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /home/deploy/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-schedule'
						    )],
			                            usePromotionTimestamp: false,
			                            useWorkspaceInPromotion: false,
			                            verbose: true)])
                }
	    }
    }
 }
}
