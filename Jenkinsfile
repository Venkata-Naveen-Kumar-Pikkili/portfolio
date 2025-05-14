pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        S3_BUCKET = 'chaithanya-portfolio-web'
        LAMBDA_CONTACT = 'ContactFormSubmissions'
        LAMBDA_VISITOR = 'trackVisitor'
        CLOUDFRONT_DIST_ID = 'YOUR_DISTRIBUTION_ID'  // Replace with actual ID
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Venkata-Naveen-Kumar-Pikkili/portfolio.git'
            }
        }

        stage('Package & Deploy Lambda: Contact Form') {
            steps {
                sh '''
                mkdir -p build
                cp lambda/contact/lambda_function.py build/
                cd build
                zip contact.zip lambda_function.py
                aws lambda update-function-code \
                    --function-name "$LAMBDA_CONTACT" \
                    --zip-file fileb://contact.zip \
                    --region "$AWS_REGION"
                cd ..
                rm -rf build
                '''
            }
        }

        stage('Package & Deploy Lambda: Visitor Logger') {
            steps {
                sh '''
                mkdir -p build
                cp lambda/track/lambda_function.py build/
                cd build
                zip visitor.zip lambda_function.py
                aws lambda update-function-code \
                    --function-name "$LAMBDA_VISITOR" \
                    --zip-file fileb://visitor.zip \
                    --region "$AWS_REGION"
                cd ..
                rm -rf build
                '''
            }
        }

        stage('Upload Frontend to S3 & Invalidate Cache') {
            steps {
                sh '''
                aws s3 sync frontend/ s3://$S3_BUCKET --delete --region "$AWS_REGION"
                aws cloudfront create-invalidation \
                    --distribution-id "$CLOUDFRONT_DIST_ID" \
                    --paths "/*" \
                    --region "$AWS_REGION"
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
