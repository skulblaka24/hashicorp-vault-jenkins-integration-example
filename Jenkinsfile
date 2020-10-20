def projectProperties = [
    [$class: 'BuildDiscarderProperty',strategy: [$class: 'LogRotator', numToKeepStr: '3']],
]
properties(projectProperties)
pipeline {
  agent any
  stages { 
    stage('Build') {
      steps {
        withVault(configuration: [engineVersion: 2, skipSslVerification: true, timeout: 60, vaultCredentialId: 'vault-approle-access', vaultUrl: 'https://v3.starfly.fr:8200'], vaultSecrets: [[path: 'kv/teb', secretValues: [[envVar: 'SECRET', vaultKey: 'token']]]]) {
        sh '''
          set -x
          export VAULT_TOKEN=$SECRET
          export VAULT_ADDR="https://v3.starfly.fr:8200"
          export VAULT_SKIP_VERIFY="true"
          ./vault status
          ./vault operator raft list-peers         
        '''
        }
      }
    }
    stage('Cleanup Build Stage') {
      steps {
          sh 'unset VAULT_TOKEN && unset VAULT_ADDR'
      }
    }
    stage('Test') {
      steps {
        withVault(configuration: [engineVersion: 2, skipSslVerification: true, timeout: 60, vaultCredentialId: 'vault-token-access', vaultUrl: 'https://v3.starfly.fr:8200'], vaultSecrets: [[path: 'kv/teb', secretValues: [[envVar: 'SECRET', vaultKey: 'token']]]]) {
        sh '''
          set -x
          export VAULT_TOKEN=$SECRET
          export VAULT_ADDR="https://v3.starfly.fr:8200"
          export VAULT_SKIP_VERIFY="true"
          ./vault status
          ./vault operator raft list-peers         
        '''
        }
      }
    }
    stage('Cleanup Test Stage') {
      steps {
          sh 'unset VAULT_TOKEN && unset VAULT_ADDR'
      }
    }
    stage('Integration Tests') {
      steps {
      sh 'curl -o vault.zip https://releases.hashicorp.com/vault/1.5.4/vault_1.5.4_linux_amd64.zip ; yes | unzip vault.zip'
        withCredentials([[$class: 'VaultTokenCredentialBinding', credentialsId: 'vault-token-access', vaultAddr: 'https://v3.starfly.fr:8200']]) {
        sh '''
          set -x
          export VAULT_SKIP_VERIFY="true"
          ./vault status
          ./vault operator raft list-peers
        '''
        }
      }
    }
  }
}
