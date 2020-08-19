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
					                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-schedule',
					                    execTimeout: 120000,
					                    flatten: false,
					                    makeEmptyDirs: false,
					                    noDefaultExcludes: false,
					                    patternSeparator: '[, ]+',
					                    remoteDirectory: '/tmp',
					                    remoteDirectorySDF: false,
					                    removePrefix: 'dist/',
					                    sourceFiles: 'dist/trainSchedule.zip')],
			                            usePromotionTimestamp: false,
			                            useWorkspaceInPromotion: false,
			                            verbose: false
				    		    )
			                    ] )
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
					                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo systemctl start train-schedule',
					                    execTimeout: 120000,
					                    flatten: false,
					                    makeEmptyDirs: false,
					                    noDefaultExcludes: false,
					                    patternSeparator: '[, ]+',
					                    remoteDirectory: '/tmp',
					                    remoteDirectorySDF: false,
					                    removePrefix: 'dist/',
					                    sourceFiles: 'dist/trainSchedule.zip')],
			                            usePromotionTimestamp: false,
			                            useWorkspaceInPromotion: false,
			                            verbose: false)])
                }
	    }
    }
 }
}
