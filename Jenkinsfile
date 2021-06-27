def secrets = [
  [path: 'secret/test/test1', engineVersion: 2, secretValues: [
    [envVar: 'PRIVATE_TOKEN', vaultKey: 'one']
]
def configuration = [vaultUrl: 'http://vault0:8200',  vaultCredentialId: 'vault-approle', engineVersion: 2]
                      
pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
    }
    stages{   
      stage('Vault') {
        steps {
          withVault([configuration: configuration, vaultSecrets: secrets]) {
            sh "echo ${env.PRIVATE_TOKEN}"
          }
        }  
      }
    }
}