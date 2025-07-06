pipeline {
    agent any
    
    environment {
        TF_DIR = 'infrastructure/terraform'
        ANSIBLE_DIR = 'infrastructure/ansible'
    }
    
    stages {
        stage('Terraform Validate') {
            steps {
                sh 'terraform -chdir=${TF_DIR} init'
                sh 'terraform -chdir=${TF_DIR} validate'
            }
        }
        
        stage('Ansible Lint') {
            steps {
                sh 'ansible-lint ${ANSIBLE_DIR}/setup.yml'
                sh 'ansible-lint ${ANSIBLE_DIR}/jenkins.yml'
            }
        }
        
        stage('Deploy Staging') {
            steps {
                withCredentials([
                    string(credentialsId: 'YC_TOKEN', variable: 'YC_TOKEN'),
                    string(credentialsId: 'YC_CLOUD_ID', variable: 'YC_CLOUD_ID'),
                    string(credentialsId: 'YC_FOLDER_ID', variable: 'YC_FOLDER_ID')
                ]) {
                    sh '''
                    terraform -chdir=${TF_DIR} apply -auto-approve
                    ansible-playbook -i ${ANSIBLE_DIR}/inventory.yml ${ANSIBLE_DIR}/setup.yml
                    ansible-playbook -i ${ANSIBLE_DIR}/inventory.yml ${ANSIBLE_DIR}/jenkins.yml
                    '''
                }
            }
        }
        
        stage('Cleanup') {
            when {
                expression { params.DESTROY_AFTER_BUILD }
            }
            steps {
                sh 'terraform -chdir=${TF_DIR} destroy -auto-approve'
            }
        }
    }
}