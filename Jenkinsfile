pipeline {
    
    agent { label 'agent' }
    stages {
        stage('pull_rep') {
            steps {
                sh ' cd /opt/ansible-exam2 && git pull origin main '
            }
        }
        stage('deploy_webapp') {
            steps {
                echo 'Deploying web application...'
                ansiblePlaybook(
                    vaultCredentialsId: 'AnsibleVault',
                    inventory: '/opt/ansible-exam2/inventory.yaml',
                    playbook: '/opt/ansible-exam2/site.yaml'
                )
            }
        }
        stage('test') {
            steps {
                script {
                    try {
                        def response = sh(returnStdout: true, script: 'curl -s -o /dev/null -w "%{http_code}" http://192.168.225.131:5000')
                        if (response.equals("200")) {
                        } else {
                            sh "exit 1"
                       }
                    } catch (e) {
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
    } 
}
