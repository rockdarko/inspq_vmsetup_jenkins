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
        stage ('Déployer') {
            steps {
                sh "ansible-playbook -i /cellardoor/env/${env.ENV}/${env.ENV}.hosts -l ${env.HOST} -e fail2ban_state=${env.STATE} -e fail2ban_ssh_port=${env.SSHPORT} -e fail2ban_ssh_logpath=${env.LOGPATH} -e fail2ban_ssh_maxretry=${env.MAXRETRY} -e fail2ban_ssh_bantime=${env.BANTIME} -e fail2ban_ssh_findtime=${env.FINDTIME} -e copyconfig=${env.COPYCONFIG} deploy.yml"
            }
        }

    }

}