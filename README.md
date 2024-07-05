# CI/CD from GitHub to AWS | Deploy Static Website from GitHub to S3 | GitHub Actions
Introduction
Continuous Integration and Continuous Deployment (CI/CD) are crucial practices in modern software development. They ensure that code changes are automatically tested, integrated, and deployed. In this guide, we'll demonstrate how to set up a CI/CD pipeline using GitHub Actions to deploy a static website to an AWS S3 bucket.

# Prerequisites
Before we begin, ensure you have the following:

- An AWS account
- An S3 bucket created in your AWS account (my-demo-website-bucket)
- Basic understanding of GitHub and GitHub Actions

# Step 1: Create an S3 Bucket
### 1. Log in to AWS Management Console:

- Navigate to the S3 service.
- Click on "Create bucket" 

### 2. Configure the Bucket:

- Bucket name: Use the name my-demo-website-bucket
- Region: us-east-1

### 3. Set Permissions:

- Ensure your bucket has the necessary permissions. For a public static website, you'll need to adjust the bucket policy and make the objects publicly readable.

### 4. Enable Static Website Hosting:

- Go to the "Properties" tab of your bucket.
- Enable "Static website hosting" and specify the index and error documents (e.g., index.html and error.html).

# Step 2: Configure AWS Credentials in GitHub
### 1. Generate AWS Access Keys:

- Go to the IAM service in the AWS Management Console.
- Create a new user with programmatic access.
- Attach the AmazonS3FullAccess policy to this user.
- Save the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.

### 2. Add Secrets to GitHub:

- Go to your GitHub repository.
- Navigate to Settings > Secrets and variables > Actions.
- Click on New repository secret and add the following secrets:
-  -  AWS_ACCESS_KEY_ID
-  - AWS_SECRET_ACCESS_KEY
-  - AWS_REGION (e.g., us-east-1)

# Step 3: Set Up GitHub Actions Workflow
### 1. Create a Workflow File:

- In your repository, create a directory named .github/workflows.
- Inside this directory, create a file named deploy.yml

### 2. Define the Workflow:

```yaml
name: Deploy to S3

on:
  push:
    branches:
      - main  # Change this to your default branch

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}

    - name: Sync files to S3
      run: |
        aws s3 sync . s3://my-demo-website-bucket --delete
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```



# Step 4: Deploy Your Website
### Commit and Push:
- git commit -am"Added file to git"
- git push origin main

Commit the deploy.yml file to your repository.
Push the changes to the main branch.
### Verify Deployment:
- Once the workflow runs successfully, your static website should be deployed to the S3 bucket.
- You can access your website using the S3 bucket URL or configure a custom domain.
