# AWS CodeSuite and Veracode

How to setup an AWS CodeBuild project with Veracode Static and SCA Analysis.

## Overview
A complete guide on setting up an Amazon Web Services Demo Account that uses Veracode Services.

Total time for implementing is approximately 4-8 hours. 

For this demonstration we will use the PetStoreAPI written in Python.

AWS offers 12 months of free usage for t3.Micro instances, up to 750 hours per month. You are required to enter a credit card number, but if you keep things small, you won't see a charge.

Veracode integrates with products in the AWS CodeSuite, specifically CodeBuild and CodePipeline.

### AWS products we’ll use:

* Cloud9 IDE (very fun and powerful IDE)
* CodeCommit - this is where we’ll place the PetStoreAPI code.
* CodeBuild - this is the primary area we integrate Veracode commands. 
* CodePipeline - pull in the CodeBuild steps to the appropiate point in your CodePipeline.

### General Flow
* Create CodeCommit Repository 
* Create Cloud9 IDE environment
* Create S3 bucket for artifact storage
* Clone PetStoreAPI 
* Push PetStoreAPI to AWS CodeCommit.
* Create AWS CodeBuild project to build the app
* Create VC API secrets and store in Secrets Manager
* Create DockerHub account
* Create VC SAST Policy build project 
* Add a pass / fail check
* Create VC SAST Pipeline build project 
* Use a baseline file
* Create VC SCA Agent build project for the app itselft
* Create VC SCA Agent build project for Docker Image
* Setup and run a DAST scan


## Step 1 Initial Setup

We'll need an AWS account and a Veracode subscription.  Costs are low for running Cloud9, CodeBuild, and CodePipeline projects and will be in the free tier for this excerise. 

https://console.aws.amazon.com/

Once signed up, navigate to CodeCommit, and create a repository called PetStoreAPI

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/1-AWSCodeCommit.png)

Note the Clone URL for later use.
Now we need to get the PetStoreAPI code into AWS CodeCommit. One of the easier ways of doing this is to use an AWS Cloud9 IDE.

Navigate to Cloud9 and create a new environment. Defaults are fine.
Lets create an S3 bucket we can use to store build artifacts and Veracode results.
Once Cloud9 is up, copy and paste this command into the terminal to create your S3 bucket. It will need to be a unique bucket name not used by anyone else.


![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/2-AWSCloud9.png)

To make Cloud9 Terminal easier to use, you can go to Preferences - User Settings - Terminal to change the font, and you can also maximize the terminal window.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/3-AWSCloud9.png)

Now use the termal to run this command to create your S3 bucket.  Be sure to customize the bucket name.

```bash
aws s3 mb s3://petstoreapi(InsertRandonNumberLetter)
```

To clone the PetstoreAPI into AWS we need to run a few commands.

Grab the Clone URL from your CodeCommit repo , such as https://git-codecommit.us-east-1.amazonaws.com/v1/repos/PetStoreAPI2

Now we need to configure git within your Cloud9 IDE and integrate it with your CodeCommit repository.

You don’t need to use your true name or email.

```bash
git config --global user.name "Me"
git config --global user.email me@me.com
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/5-AWSCloud9.png)

Now, we are ready to clone our AWS repository to our Cloud9 using the following terminal command -

Update your Repo URL.

```bash
git clone https://git-codecommit.us-east-1.amazonaws.com/v1/repos/PetStoreAPI2
```

It will tell you the repository is empty.

Clone PetStoreAPI from the official Veracode repo, and then files into our AWS repo.

```bash
git clone https://github.com/veracode/petstore-api-flask
cp -r ~/environment/petstore-api-flask/* ~/environment/PetStoreAPI/
```

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/5-AWSCloud9.png)

## Pushing code to AWS CodeCommit
After saving the file, change directories to the new repository directory, and update the branch to main if not already:

```bash
cd ~/environment/PetStoreAPI/
git branch -m master main
git add .
git commit -m "bringing in the code"
git push
```

Open another tab in your browser and keep the Cloud9 IDE open:

https://console.aws.amazon.com/

Navigate to the CodeCommit service, and click on PetStoreAPI. You will see the source code has been uploaded. Now we can setup CodeBuild projects.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/6-AWSCloud9.png)

Click on CodeBuild on the left navigation menu.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/8-AWSCodeBuild.png)


![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/9-AWSCodeBuild.png)

Point this build project to the PetStoreAPI CodeCommit Repo.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/AWSCodeCommitEditSource.png)

Use the managed image option with the below settings.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/17AWSCodeBuild.png)

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/20-AWSCodeBuild.png)


Select the Insert Build Commands option, and then replace the default with the optimized buildspec below.   Since this is Python, our build step will simply ZIP the files. AWS expects artifacts to be passed between CodePipeline steps as zip files. This will archive the file to the S3 bucket we created.  This will download the source, zip it up, and then store as an artifact in your S3 bucket to pull.

```bash
version: 0.2

phases:
  build:
    commands:
      - zip petstoreapi.zip *
artifacts:
  files:
    - petstoreapi.zip
```

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/21-AWSCodeBuild.png)

Set the Artifacts to the S3 bucket.  If no name entered into the Name section, it will create a sub folder with the CodeBuild project name.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/22-AWSCodeBuild.png)

Everything else is default. Create the Build Project, and then start the project to see if it works.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/23-AWSCodeBuild.png)

Here you can see the artifact in your S3 bucket.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/InitialSetup/24-AWSCodeBuild.png)

Move onto the next step, setting up the VID, VKEY, and SRCCLR_API_TOKEN in the AWS Secrets Manager