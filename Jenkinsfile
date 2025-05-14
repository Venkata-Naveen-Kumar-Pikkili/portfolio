pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        S3_BUCKET = 'chaithanya-portfolio-web'
        LAMBDA_CONTACT = 'ContactFormSubmissions'
        LAMBDA_VISITOR = 'trackVisitor'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Venkata-Naveen-Kumar-Pikkili/portfolio.git'
            }
        }

        stage('Deploy Lambda: Contact Form') {
            steps {
                sh '''
                zip -j contact.zip lambda/contact/lambda_function.py
                aws lambda update-function-code \
                    --function-name $LAMBDA_CONTACT \
                    --zip-file fileb://contact.zip \
                    --region $AWS_REGION
                '''
            }
        }

        stage('Deploy Lambda: Visitor Logger') {
            steps {
                sh '''
                zip -j visitor.zip lambda/track/lambda_function.py
                aws lambda update-function-code \
                    --function-name $LAMBDA_VISITOR \
                    --zip-file fileb://visitor.zip \
                    --region $AWS_REGION
                '''
            }
        }

        stage('Upload Frontend to S3') {
            steps {
                sh '''
                aws s3 sync ./frontend s3://$S3_BUCKET --delete
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}