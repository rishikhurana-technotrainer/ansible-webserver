pipeline {
  agent { label "agentfarm" }
stages {
		stage('Delete the workspace') {
			steps {
				cleanWs()
			}
    }
  
  stage('Second Stage') {
			steps {
				echo "Second Stage"
			}
    }
  
  
  stage('Third Stage') {
			steps {
				echo "Third Stage"
			}
    }
  }

}
