# GITHUB-ACTION-YAML-FORECR-PUSH


name: Deploy to ECR

on:
 
  push:
    branches: [ main ]

jobs:
  
  build:
    
    name: Build Image
    runs-on: ubuntu-latest

   
    steps:

    - name: Check out code
      uses: actions/checkout@v2
    
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.ACCESSKEY }}
        aws-secret-access-key: ${{ secrets.SECRETKEY }}
        aws-region: us-east-2

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Build, tag, and push image to Amazon ECR
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: spring-boot
        
      run: |
            docker build -t spring-boot:latest-0.14${{ env.GITHUB_RUN_NUMBER }} . 
            docker tag spring-boot:latest-0.14${{ env.GITHUB_RUN_NUMBER }} 733215289643.dkr.ecr.us-east-2.amazonaws.com/spring-boot:latest-0.14${{ env.GITHUB_RUN_NUMBER }}
            docker push 733215289643.dkr.ecr.us-east-2.amazonaws.com/spring-boot:latest-0.14${{ env.GITHUB_RUN_NUMBER }}
