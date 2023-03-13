pipeline {
    agent any
    
    environment {
        HEROKU_APP_NAME = "maruma-morandini"
        AWS_REGION = "us-east-1"
        S3_BUCKET_NAME = "hackademy-practice"
        DOCKER_IMAGE_NAME = "hackademy-app"
    }
    
    stages {
        stage("Clone repository") {
            steps {
                checkout scm
            }
        }
        
        stage("Build and push Docker image") {
            when {
                branch 'master'
            }
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh "aws s3 cp s3://${S3_BUCKET_NAME}/${DOCKER_IMAGE_NAME} ./${DOCKER_IMAGE_NAME}"
                    sh "docker load -i ./${DOCKER_IMAGE_NAME}"
                    sh "docker tag ${DOCKER_IMAGE_NAME} registry.heroku.com/${HEROKU_APP_NAME}/web"
                    sh "heroku container:login"
                    sh "heroku container:push web -a ${HEROKU_APP_NAME}"
                    sh "heroku container:release web -a ${HEROKU_APP_NAME}"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
    
    triggers {
        pollSCM('H/5 * * * *')
    }
    
    options {
        disableConcurrentBuilds()
    }
}
