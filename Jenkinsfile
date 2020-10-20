def projectProperties = [
    [$class: 'BuildDiscarderProperty',strategy: [$class: 'LogRotator', numToKeepStr: '3']],
]
properties(projectProperties)
pipeline {
  agent any
  stages { 
    stage('Cleanup') {
      steps {
          sh 'echo Cleanup'
      }
    }
    stage('Build') {
      steps {
        echo 'Build Successful!'
      }
    }
    stage('Test') {
      steps {
          sh 'echo Test'
      }
    }
    stage('Integration Tests') {
      steps {
      sh 'curl -o vault.zip https://releases.hashicorp.com/vault/1.5.4/vault_1.5.4_linux_amd64.zip ; yes | unzip vault.zip'
        withCredential([VaultAppRoleCredential(credentialsId: 'jenkins-vault', roleId: 'ROLE', secretId: 'SECRET')]) {
        sh '''
          set -x
          export VAULT_SKIP_VERIFY="true"
          export VAULT_ADDR=https://v1.starfly.fr:8200
          export SECRET_ID=$(./vault write -field=secret_id -f auth/approle/role/java-example/secret-id)
          export VAULT_TOKEN=$(./vault write -field=token auth/approle/login role_id=${ROLE} secret_id=${SECRET})
          echo $VAULT_ADDR
        '''
        }
      }
    }
  }
  environment {
    mvnHome = 'maven-3.2.5'
  }
}
