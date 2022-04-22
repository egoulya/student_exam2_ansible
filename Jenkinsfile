pipeline {
    
    agent { label 'agent' }
    stages {
        stage('pull_rep') {
            steps {
                sh ' cd /opt/student_exam2_ansible && git pull origin main '
            }
        }
        stage('deploy_webapp') {
            steps {
                echo 'Deploying web application...'
                ansiblePlaybook(
                    vaultCredentialsId: 'AnsibleVault',
                    inventory: '/opt/student_exam2_ansible/inventory.yaml',
                    playbook: '/opt/student_exam2_ansible/site.yaml',
                    disableHostKeyChecking: true
                )
            }
        }
        stage('test') {
            steps {
                script {
                    try {
                        def response = sh(returnStdout: true, script: 'curl -s -o /dev/null -w "%{http_code}" http://192.168.225.131:5000')
                        if (response.equals("200")) {
                            echo "Success: HTTP Status: ${response}"
                        } else {
                            echo "Failure: HTTP Status: ${response}"
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
