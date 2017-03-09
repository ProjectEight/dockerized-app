#!/usr/bin/env groovy

node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:ProjectEight/dockerized-app.git'

            // Start services (Let docker-compose build containers for testing)
            sh "./sd-develop up -d"

            // Get composer dependencies
            sh "./sd-develop composer install"

            // Create .env file for testing
            // sh '/var/lib/jenkins/.venv/bin/aws s3 cp s3://sd-secrets/env-ci .env --region eu-west-2'

            sh './sd-develop art key:generate'
        }

        stage('test') {
            sh "APP_ENV=testing ./sd-develop test"
        }

        if( env.BRANCH_NAME == 'master' ) {
            stage('package') {
                sh './docker/build'
            }
        }
    } catch(error) {
        // Maybe some alerting?
        sh 'echo "Error encountered."'
        throw error
    } finally {
        // Spin down containers no matter what happens
        sh './sd-develop down'
    }
}