# AWS Lambda RAG serverless function with Pinecone vector database
This repo provides RAG serverless function connected with Pinecone vector database. The user can upload PDF files to Pinecone and input user prompts to this function to extract useful information.  
## Docker
The AWS Lambda function is built with Docker. This repo provides Dockerfile and requirements.txt to build the Docker image.  
First, please make sure that you have set up your IAM user with AWS CLI, please refer to https://docs.aws.amazon.com/cli/latest/userguide/cli-authentication-user.html for instructions.  
If this is the first time you start with IAM, please make sure that you add AmazonEC2ContainerRegistryFullAccess and AmazonECS_FullAccess to your IAM user.  
And set up your Amazon ECR with the reference of https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html for general instructions. Then, you are ready to build and push your docker image to ECR:  
```bash
git clone https://github.com/issacchan26/TalkToPDF.git
cd TalkToPDF
```
Authenticate to your default registry
```bash
aws ecr get-login-password --region your_region | docker login --username AWS --password-stdin your_aws_account_id.dkr.ecr.region.amazonaws.com
```
Create a repository named 'lambda-docker-deploy' with your region
```bash
aws ecr create-repository --repository-name lambda-docker-deploy --region your_region
```
Build your docker image
```bash
docker build -t lambda-docker-deploy .
```
Tag and push your docker image to ECR
```bash
docker tag lambda-docker-deploy:latest your_aws_account_id.dkr.ecr.region.amazonaws.com/lambda-docker-deploy:latest
```
```bash
docker push your_aws_account_id.dkr.ecr.region.amazonaws.com/lambda-docker-deploy:latest
```
Finally, you can deploy your AWS Lambda function by selecting your container image in ECR.
## Create API endpoint
After deploying your AWS Lambda function, you can create the API endpoint by adding trigger of API Gateway, creating new HTTP API and configure Security to 'open'.
## Usage with curl
```
curl [options...] [url]  
-X, request command, this function supports POST request  
-H, content type, which is "Application/JSON"  
-d, data, here we enter our userprompt as data  
url, API endpoints  
```
Example:
```bash
curl -X POST -H 'content-type: application/json' -d '{ "userprompt": "Who published Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks?" }' https://your_api_endpoints_of_talktopdf/ 
```
Enter your question by replacing the value of userprompt in option -d above, e.g.
```bash
curl -X POST -H 'content-type: application/json' -d '{ "userprompt": "What is RAG?" }' https://your_api_endpoints_of_talktopdf/
```
