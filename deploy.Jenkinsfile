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
                sh "ansible-playbook -i /cellardoor/env/${env.ENV}/${env.ENV}.hosts -l ${env.HOST} -e action=install -e fail2ban_state=true -e fail2ban_ssh_port=22 -e fail2ban_ssh_logpath=/var/log/auth.log -e fail2ban_ssh_maxretry=3 -e fail2ban_ssh_bantime=300 -e fail2ban_ssh_findtime=3000 -e copyconfig=false deploy.yml"
            }
        }

    }

}