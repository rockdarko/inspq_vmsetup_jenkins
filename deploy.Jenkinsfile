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
                sh "ansible-playbook -i /cellardoor/env/${env.ENV}/${env.ENV}.hosts -l ${env.HOST} -e action=install -e fail2ban_state=true -e fail2ban_ssh_port=22 -e fail2ban_ssh_logpath=/var/log/auth.log -e fail2ban_ssh_maxretry=3 -e fail2ban_ssh_bantime=300 -e fail2ban_ssh_findtime=3000 -e copyconfig=false -e sudoers_user=${env.SUDOERS} deploy.yml"
            }
        }
        stage ('Nexus Login') {
            steps {
				script {
					try {
		            	timeout (time: 1, unit: "HOURS" ){
							DEPLOY_DOCKERLOGIN = input(
		                		id: 'docker_login',
		                		message: "Continu?",
		                		parameters: [ 
		                			[$class: 'ChoiceParameterDefinition', 
		                			choices: [ 'yes','no' ].join('\n'), 
		                			name: 'tag'] 
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
                	if ( "${DEPLOY_DOCKERLOGIN}" == "oui" ) {			
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