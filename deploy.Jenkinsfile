#!/usr/bin/env groovy

pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        disableConcurrentBuilds()
    }

    stages {
        stage ('Checkout') {
            steps {
                checkout scm
            }
        }
        stage ('Configurer Ansible') {
            steps {
	            sh "rm -rf roles"
	            sh "ansible-galaxy install -f -r requirements.yml"        	    
            }
        }
        stage ('Deploy') {
            steps {
                sh "ansible-playbook -i /cellardoor/env/${env.ENV}/${env.ENV}.hosts -l ${env.HOST} -e action=install -e fail2ban_state=true -e fail2ban_ssh_port=${env.FAIL2BAN_SSH_PORT} -e fail2ban_ssh_logpath=${env.FAIL2BAN_SSH_LOGPATH} -e fail2ban_ssh_maxretry=${env.FAIL2BAN_MAXRETRY} -e fail2ban_ssh_bantime=${env.FAIL2BAN_BANTIME} -e fail2ban_ssh_findtime=${env.FAIL2BAN_FINDTIME} -e copyconfig=false -e sudoers_user=${env.SUDOERS} -e docker_dns=${env.DOCKER_DNS} deploy.yml"
            }
        }
        stage ('Nexus Login') {
            steps {
				script {
					try {
		            	timeout (time: 1, unit: "HOURS" ){
							DEPLOY_DOCKERLOGIN = input(
		                		id: 'docker_login',
		                		message: "Continue?",
		                		parameters: [ 
		                			[$class: 'ChoiceParameterDefinition', 
		                			choices: [ 'yes','no' ].join('\n'), 
		                			name: 'Select:'] 
		                		]
		                	)
		                } 
		            }
                    catch (err) {
                	    DEPLOY_DOCKERLOGIN = "no"
                	}
                    if ( "${DEPLOY_DOCKERLOGIN}" == "no" ) {
                	    currentBuild.getRawBuild().getExecutor().interrupt(Result.SUCCESS)
                	}
                	if ( "${DEPLOY_DOCKERLOGIN}" == "yes" ) {			
			        	sh "ansible-playbook -i /cellardoor/env/${env.ENV}/${env.ENV}.hosts -l ${env.HOST} docker_login.yml"
				        	}
		        	else {
		        	    currentBuild.getRawBuild().getExecutor().interrupt(Result.SUCCESS)
		        	}
		        }
			}
        }
        

    }

}