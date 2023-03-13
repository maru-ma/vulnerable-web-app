pipeline {
    agent any
    stages {
        stage('Clone code') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker image') {
            steps {
                sh 'docker build -t hackademy-app .'
            }
        }
        stage('Push Docker image to S3') {
            steps {
                withAWS(region: 'us-east-1', credentials: 'aws-credentials') {
                    s3Upload(bucket: 'hackademy-images', path: 'hackademy-app:latest', file: 'Dockerfile')
                }
            }
        }
        stage('Deploy to Heroku') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'heroku1', passwordVariable: 'HEROKU_API_KEY', usernameVariable: 'HEROKU_EMAIL')]) {
                    sh 'heroku container:login'
                    sh 'docker tag hackademy-app registry.heroku.com/hackademy-app/web'
                    sh 'docker push registry.heroku.com/hackademy-app/web'
                    sh 'heroku container:release web --app hackademy-app'
                }
            }
        }
    }
}
