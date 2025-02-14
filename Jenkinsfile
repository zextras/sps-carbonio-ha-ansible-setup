pipeline {
    agent {
        node {
            label 'infra-agent-v3'
        }
    }

    options {
        skipDefaultCheckout()
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 1, unit: 'HOURS')
    }
    
    stages {

        stage('Checkout') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                checkout scm
            }
        }

        stage('Build Carbonio HA Collections') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                sh '''
                  ansible-galaxy collection build carbonio_kafka --output-path builds
                  ansible-galaxy collection build carbonio_ldap --output-path builds
                  ansible-galaxy collection build carbonio_patroni --output-path builds
                '''
            }
        }

        stage('Publish Carbonio HA Collections') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                withCredentials([string(credentialsId: 'ansible-galaxy-token', variable: 'galaxy_token')]) {
                    sh '''
                      for artifact in builds/*.tar.gz; do
                        echo "Publishing $artifact ..."
                        ansible-galaxy collection publish "$artifact" --token $galaxy_token
                      done
                    '''
                }
            }
        }
    }
}