def projectProperties = [
    [$class: 'BuildDiscarderProperty',strategy: [$class: 'LogRotator', numToKeepStr: '3']],
]
properties(projectProperties)
pipeline {
  agent any
  stages { 
    stage('Cleanup') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn clean'
        }
        
      }
    }
    stage('Test') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn test'
        }
        
      }
    }
    stage('Compile') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn compile'
        }
        
      }
    }
    stage('Package') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn package'
        }
        
      }
    }
    stage('Notify') {
      steps {
        echo 'Build Successful!'
      }
    }
    stage('Integration Tests') {
      steps {
      sh 'curl -o vault.zip https://releases.hashicorp.com/vault/1.5.4/vault_1.5.4_linux_amd64.zip ; yes | unzip vault.zip'
        withCredentials([string(credentialsId: 'jenkins-vault-approle', variable: 'roleId'),string(credentialsId: 'jenkins-vault-approle', variable: 'secretId')]) {
        sh '''
          set -x
          curl https://raw.githubusercontent.com/ncorrare/vault-java-example/master/ca.crt > ca.crt
          export VAULT_SKIP_VERIFY="true"
          export VAULT_ADDR=https://v1.starfly.fr:8200
          export SECRET_ID=$(./vault write -field=secret_id -f auth/approle/role/java-example/secret-id)
          export VAULT_TOKEN=$(./vault write -field=token auth/approle/login role_id=${roleId} secret_id=${secretId})
          keytool -import -trustcacerts -file ca.crt -alias CorrarelloCA -keystore cacerts -noprompt -keypass changeit -storepass changeit
          java -D"javax.net.ssl.trustStore=./cacerts" -jar target/java-client-example-1.0-SNAPSHOT-jar-with-dependencies.jar 
        '''
        }
      }
    }
  }
  environment {
    mvnHome = 'maven-3.2.5'
  }
}
