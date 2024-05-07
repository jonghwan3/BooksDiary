# Books Diary
We used AWS to serve our service serverless. \
Our service helps clients to search books, save books, update comments and ratings, get recommendations and so on. 

### System Architecture
![system_architecture](https://github.com/jonghwan3/BooksDiary/assets/97586094/b2197774-16e5-4f48-884f-d99ee322a0e2)

As we have uploaded detailed report and demo already, we will skip detailed explanations here. \
However, I will mainly focus on how to configure environments on AWS and how to run our service here.

# Configuration and Running our service

This guide will explain steps to configure AWS environment.

## Prerequisites

Before getting started, ensure you have the following prerequisites
- AWS Account : free-tier (minimum)
- SSH Key Pairs : using ssh-keygen, save public and private keys
   
## Configuration 
1. [CloudFormation](#CloudFormation)
2. [EC2](#EC2)
3. [S3 Bucket](#Bucket)
4. [Lambda functions](#Lambda)
5. [API Gateway](#APIGateway)
6. [SES](#SES)
7. [IAM roles](#IAM)
## 1. CloudFormation <a name="CloudFormation"></a>

On AWS - CloudFormation, create new stack with new resources.
- Replace some arguments on `AWS_CloudFormation_debian.yaml` (indicated on comments)
- Choose an existing template, Upload a template file (`AWS_CloudFormation_debian.yaml` on my repository)
- Enter stack name and click next serveral times and submit with aceepting acknowledgement.
  

## 2. EC2 <a name="EC2"></a>

On AWS - EC2, we can check a new EC2 has been created.
- Check Public Ipv4 address (e.g., 3.147.47.36)
 
## 3. S3 Bucket <a name="Bucket"></a>

On AWS - S3 Bucket, we will upload all files in `html`
- Create bucket
- On the bucket, upload with Add files (`html/~.html` files)
- Upload with folders (`html/css`, `html/images`, `html/webfonts`)
- On the bucket - Properties - Static website hosting - Edit : Index document : `login.html`
- Edit Bucket Policy : In the Permissions tab of your static content bucket, click on Bucket policy. put * following yourwebsite-static-content on Resource

## 4. Lambda functions <a name="Lambda"></a>

On AWS - Lambda, we will create `nodejs/~.js` files
- Create functions with Runtime : Node.js 20.x, Architecture : x86_64
- Create two layers on AWS - Lambda - Layers by uploading a .zip file (`package_json/~.zip` individually)
- Add a custom layer created by `mongoDB_uuid.zip` to every Lambda functions
- Add a custom layer created by `stop_word` to `recommendBooks.js` Lambda function.
- Copy and Paste from `nodejs/~.js` files to Lambda functions (**Do not forget clicking deploy after changes**)
- On each Lambda function, Configuration - Edit Environment variables. (Key : MONGODB_URI, Value : mongodb://admin:vBTaTtAqabuC4NvV7jm3@{EC2 Ipv4 address}:27017)
- On `register.js` and `forget.js`, add another Environment variables. (Key : ADMIN_URI, Value : {your email})
  

## 5. API Gateway <a name="APIGateway"></a>
On AWS - API Gateway, create each API mapping to Lambda function
- Build REST API
- Create resource with appropriate name (**Enable CORS!**)
- For each resource, create POST method (Lambda function, enable Lambda proxy integration, and select appropriate Lambda function)
- For each resource, click **Deploy API** button
- Invoke URL can be checked in API Gateway - APIs - your API - Stages, which will be used on your html files

## 6. SES <a name="SES"></a>
On AWS - SES, verify your sender email and recipient email
- On SES - Configuration - Identities, Click Create Identity, and put your Email address and Create Identity
- The previous step will give you verification url via email (As Amazon requires you to contact support team about sending emails wihtout registering Identities, we should manually register identities for sending admin email and receiving user emails)

## 7. IAM roles <a name="IAM"></a>
We should add permissions for `forget` and `register` lambda function to use sending emails
- On AWS - IAM - Roles - corresponding Lambda functions - Add **AmazonSESFullAccess** permissions
- Add permissions AmazonEC2FullAccess and AmazonS3FullAccess for your user





