Map vaultConfiguration = [
    $class: 'VaultConfiguration',
    vaultUrl: 'http://vault0:8200',
    vaultCredentialId: 'vault-approle'
]
List vaultSecrets = [
        [ $class: 'VaultSecret', path: "secret/jenkins/github", engineVersion: 2, secretValues: [
            [ $class: 'VaultSecretValue', envVar: 'PRIVATE_TOKEN', vaultKey: 'private-token' ],
            [ $class: 'VaultSecretValue', envVar: 'PUBLIC_TOKEN', vaultKey: 'public-token' ],
            [ $class: 'VaultSecretValue', envVar: 'API_KEY', vaultKey: 'api-key' ]
        ]]
]

pipeline {
    agent any
    environment {
        VAULT_ADDR = 'http://vault0:8200'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
    }
    stages{   
      stage('Vault') {
        steps {
          withVault([configuration: params.vaultConfiguration, vaultSecrets: vaultSecrets]){
            sh "echo ${env.PRIVATE_TOKEN}"
            sh "echo ${env.PUBLIC_TOKEN}"
            sh "echo ${env.API_KEY}"
            sh "vault status"
          }
        }  
      }
    }
}