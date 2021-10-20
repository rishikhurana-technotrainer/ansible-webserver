pipeline {
	agent { label "agentfarm" }
	environment {
		KEY_FILE = '/home/ubuntu/.ssh/vm-instance-key.pem'
		USER = 'ubuntu'
	}
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
				//sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint:4 apache-install.yml'
				//sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint:4 website-update.yml'
				//sh 'docker run --rm -v $WORKSPACE/playbooks:/data cytopia/ansible-lint:4 website-test.yml'
				
				
			}
		}
		stage('install apache update') {
			steps {
				sh 'ansible-playbook -u $USER --private-key $KEY_FILE -i $WORKSPACE/host_inventory $WORKSPACE/playbooks/apache-install.yml'
				sh 'ansible-playbook -u $USER --private-key $KEY_FILE -i $WORKSPACE/host_inventory $WORKSPACE/playbooks/website-update.yml'
			}
				
		}
		stage('Test website') {
			steps {
				sh 'ansible-playbook -u $USER --private-key $KEY_FILE -i $WORKSPACE/host_inventory $WORKSPACE/playbooks/website-test.yml'
			}
		}
	}
	post {
		stage('Send Slack Notification') {
            steps {
                slackSend color: 'warning', message: "Mr Eddie: Please approve ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.JOB_URL} | Open>)"
            }
        }
        stage('Request Input') {
            steps {
                input 'Please approve or deny'
            }
        }
	}
}
