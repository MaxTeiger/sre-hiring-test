# Aircall.io - DevOps technical test

This test is a part of our hiring process at Aircall for [DevOps positions](https://aircall.io/jobs#SystemAdministrator). It should take you between 1 and 6 hours depending on your experience.

__Feel free to apply! Drop us a line with your Linkedin/Github/Twitter/AnySocialProfileWhereYouAreActive at jobs@aircall.io__


## Summary

An intern in our team has developped an application to resize images. It's working fine.

Unfortunatly, he left the company and we have no documentation or no insights at all about
what he did.

We just have the code.

With the following request to the application, the image is resized, stored and accessible from s3.

```
curl --location --request POST 'http://resize.aircall.com/image' \
--form 'file=@img.jpg' \
--form 's3Key=img.jpg'
```

The provided code is working. 

It seems that our intern was using something called Lambda. Don't know what is it.



## What we want ?
Could you please take this code, and deploy it on AWS in a cool way?

Please, make Infra as Code because I heard that it's cool to do so.

You can also make some diagrams if needed.

Technically speaking, we need to get:

- Application URL
- URL of the resized images
- GitHub repo

## Good to know

- packages have to be builded from a linux platform (docker is your friend)
- input is not a JSON but a form data (multipart/form-data)

## Nice to have

- logs
- tracing
- deployment framework
- CI/CD
- auth

Good luck !

---
# How to use the project

## Prerequisites

Download `npm`, `node` & `make`
Download & configure `SAM CLI` along with your AWS Credentials.

If you're going to build on windows using AWS SAM Framework, you also need to download `Docker`.

## Write Code

You can write the code you want in the lambdaFunction folder which contains the Lambda Function. 

## Build 

### MacOS & Linux
To build the application using SAM, you can go in the `sam` folder. 
```bash
cd sam && sam build
``` 

### Windows
To build the application using SAM, you can go in the `sam` folder. 
```bash
cd sam && sam build -u
``` 
## Deploy

To deploy the application after launching the build command, you can run in the `sam` folder: 

```bash
sam deploy --guided
``` 

## Delete

If you want to delete the stack just run in the `sam` folder: 
```bash
sam delete
```

# CI/CD Configuration

I didn't write tests for the function, but if you configure correctly your AWS Secrets (`AWS_ACCESS_KEY_ID` & `AWS_SECRET_ACCESS_KEY`) in your repo, each time a push occurs on master, the infrasctructure should upload automaticaly.

## Test result

### Checklist
- [x] logs
- [x] tracing
- [x] deployment framework
- [x] CI/CD
- [x] Auth

### How to test your infrastructure

#### Without authentication

You can just launch `cd test_resources` and run the `curl` command
```bash
curl -v --location --request POST 'https://humxe7hjia.execute-api.eu-west-3.amazonaws.com/Prod/image' \                          
--form 'file=@test.jpg' \
--form 's3Key=test.jpg'
```

This will upload and resize the image to the bucket. 

**Note**: If you deployed the API yourself, the URL will not be the same, but it will be listed in SAM Deploy outputs

#### With authentication

To test the setup with authentification, it's a little bit more complicated, first you'll have to create an account on the Cognito User Pool deployed. 
I disable this because the infra is deployed on my personnal account. 

Before anything, just go in the `test_resources` folder. 

Assming you have an account, you'll have to initiate auth in order to retrieve an access token. To do this just update the file `/test_resources/user-data.json` and run : 
```bash
curl -X POST --data @user-data.json \
-H 'X-Amz-Target: AWSCognitoIdentityProviderService.InitiateAuth' \
-H 'Content-Type: application/x-amz-json-1.1' \
https://cognito-idp.<aws_region>.amazonaws.com/
```

If it is your first login attempt, you'll have to answer a challenge from Cognito. To do this retrieve the `Session` ID from the request body of the previous `curl` and upload the file `/test_ressources/challenge.json`. Once it's fone, run : 

```bash
curl -X POST --data @challenge.json \
-H 'X-Amz-Target: AWSCognitoIdentityProviderService.RespondToAuthChallenge' \
-H 'Content-Type: application/x-amz-json-1.1' \
https://cognito-idp.eu-west-3.amazonaws.com/
```

You should retrieve in the body of the previous request an `id_token` and an `access_token`. Here we are only interested in the `id_token`. This token gives us the permission to reach the API. 
Finaly, to access the API and launch the function, just run : 
```bash
curl -v --location --request POST 'https://14hcf1ux3e.execute-api.eu-west-3.amazonaws.com/Prod/image' \
-H "Authorization: <ID_TOKEN>"
--form 'file=@test.png' \
--form 's3Key=test.png' 
```

### Outputs
Images URL (will be disabled in the future):
	- [s3://aircall-test-s3bucket-14sjnebq5gw78/test.jpg_200](https://aircall-test-s3bucket-14sjnebq5gw78.s3.eu-west-3.amazonaws.com/test.jpg_200)
	- [https://aircall-test-s3bucket-14sjnebq5gw78.s3.eu-west-3.amazonaws.com/test.jpg_75](https://aircall-test-s3bucket-14sjnebq5gw78.s3.eu-west-3.amazonaws.com/test.jpg_75)

Application URL: 
- Without Auth: https://humxe7hjia.execute-api.eu-west-3.amazonaws.com/Prod/image (will be disabled soon)
- With Auth: https://14hcf1ux3e.execute-api.eu-west-3.amazonaws.com/Prod/image (will be disabled soon)

### Schema(s)

#### Architecture (without Auth)
![Current Schema](img/schema_simple.png)

#### Architecture (with Auth & CloudFront)
![Complete Schema](img/schema_complete.png)