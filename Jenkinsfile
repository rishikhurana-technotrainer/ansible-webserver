pipeline {
	agent { label "agentfarm" }
	stages {
		stage('Delete the workspace') {
			steps {
				cleanWs()
			}
		}
		stage('Installing Ansible') {
			steps {
				script {
					def ansible_exists = fileExists '/usr/bin/ansible'
					if (ansible_exists == true) {
						echo "Skipping Ansible"
					} else {
						sh 'sudo apt-get update -y && sudo apt-get upgrade -y'
						sh 'sudo apt install -y wget tree unzip ansible python3-pip python3-apt'
					}
				}
			}
		}
		stage('Download Ansible Code') {
			steps {
				git credentialsId: 'git-repo-creds', url: 'git@github.com:saptt/ansible-webserver.git'
			}
		}
		stage('Run ansible-lint against playbooks') {
			steps {
   				echo "$NODE_NAME"
  				sh 'ls -la /etc/ansible/roles'
				
				sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint apache-install.yml'
				sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint website-update.yml'
				sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint website-test.yml'
			}
		}
	}
}
