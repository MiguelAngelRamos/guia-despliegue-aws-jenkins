```groovy 
        stage('Deploy') {
            steps {
                echo "Deploying to Amazon S3..."
                unstash 'dist'
                withAWS(credentials: 'aws-credentials', region: "${env.AWS_REGION}") {
                    sh '''
                        apt-get update -qq && apt-get install -y -qq python3 python3-venv
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip
                        pip install awscli
                        aws s3 sync dist/ s3://$AWS_S3_BUCKET --region $AWS_REGION --delete
                    '''
                }
            }
        }
```
