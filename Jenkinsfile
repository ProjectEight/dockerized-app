#!/usr/bin/env groovy

node('master') {
    try {
        stage('build') {
            git url: 'git@github.com:ProjectEight/dockerized-app.git'

            // Start services (Let docker-compose build containers for testing)
            sh "./develop up -d"

            // Get composer dependencies
            sh "./develop composer install"

            // Create .env file for testing
            sh 'cp .env.example .env'
            sh './develop art key:generate'
            sh 'sed -i "s/REDIS_HOST=.*/REDIS_HOST=redis/" .env'
            sh 'sed -i "s/CACHE_DRIVER=.*/CACHE_DRIVER=redis/" .env'
            sh 'sed -i "s/SESSION_DRIVER=.*/SESSION_DRIVER=redis/" .env'
        }

        stage('test') {
            sh "APP_ENV=testing ./develop test"
        }

        stage('build-on-push') {
            sh 'echo "Testing that this build was initiated immediately after pushing to repo"'
            sh "APP_ENV=testing ./develop test"
        }

    } catch(error) {
        // Implement error handling, alerting, etc here
        throw error

    } finally {
        // Spin down containers no matter what happens
        sh './develop down'

    }
}