pipeline {
  agent any

  environment {
    ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
  }

  parameters {
    string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
  }

  stages {
    stage ("intiall clean up") {
      steps {
        dir("${WORKSPACE}") {
          deletedddDir()
        }
      }
    }
    stage ("SCM CHECKOUT") {
      steps {
        https://github.com/akinolafusi/ansible-config-mgt.git
      }
    }
    stage ("Preapre ansible") {
      steps {
        sh 'echo ${WORKSPACE}'
        sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'
      }
    }
    stage ("RUN PLAYBOOK") {
      steps {
        ansiblePlaybook become: true, colorized: true, credentialsId: 'ubuntu', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory/${inventory}', playbook: 'playbooks/site.yml'
      }
    }
    stage ("clean after build") {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
      }
    }
  }
}
gettttt