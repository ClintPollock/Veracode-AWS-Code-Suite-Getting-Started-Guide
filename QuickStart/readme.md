# AWS CodeSuite and Veracode

How to setup an AWS CodeBuild project with Veracode Static Pipline Scanner and Software Composition Analysis. 

## Overview
A quick start guide to getting results directly into your console.

For this demonstration we will use the PetStoreAPI written in Python.  

If you plan to use your own project, we simply need to have a ZIP file passed into the Veracode Scan. 

Double check the app is packaged properly.
https://docs.veracode.com/r/compilation_packaging

Veracode is generally used in AWSCodeBuild and AWSCodePipeline.

### AWS products we’ll use:

* CodeCommit - this is where we’ll place the PetStoreAPI code.
* CodeBuild - this is the primary area we integrate Veracode commands. 

### Prerequisites
* Veracode API Key and ID
* Veracodoe SCA Agent API key and ID

### General Flow Basic
* Create CodeCommit Repository 
* Create CodeBuild job

### Uploaidng source to a new CodeCommit repo

GitHub - veracode/petstore-api-flask: A vulnerable API based on the Swagger Petstore API, built in Flask. 

https://github.com/veracode/petstore-api-flask

![AWS Code](images/1-QuickStart.png)

In AWS navigate to CodeCommit.  Create a new repo.  
Create your first file called buildspec.yml, and past the below into it.  Then, choose the Upload File option and upload the petstore-api-flask-main.zip.

```bash
version: 0.2

phases:
  build:
    # The LS commands simply allow you to see what is in the folder.  In the previous step we created the petstoreapi.zip.
    # It was then downloaded locally based on the Source Configuration for this project to pull from the S3 bucket."
    commands:
      - ls -la
      - ls /opt/veracode/
      # Run Static Pipeline Scanner
      - curl -O https://downloads.veracode.com/securityscan/pipeline-scan-LATEST.zip
      - unzip pipeline-scan-LATEST.zip pipeline-scan.jar
      - java -jar /opt/veracode/pipeline-scan.jar -vid $vid -vkey $vkey --file petstoreapi.zip
      # Run SCA Scanner
      - curl -sSL https://download.sourceclear.com/ci.sh | sh >> Veracode-SCA-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
      - cat Veracode-SCA-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
      - java -jar VeracodeJavaAPI.jar -vid $vid -vkey $vkey -appname AWSCodeBuild-PetStoreAPI -action UploadAndScan -createprofile true -version $CODEBUILD_BUILD_ID -filepath petstore.zip
  post_build:
    # Here we are preparing the results to be archived.
    commands:
      - cp results.json Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
      - zip results.zip results.json
      - ls -la
artifacts:
  files:
    - Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
    - Veracode-SCA-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
```

Create a Build Project

Set your source location to the CodeCommit repo you just created.

![AWS Code](images/4-QuickStart.png)

In the Environment section use these options:

![AWS Code](images/4-QuickStart.png)

Expand the Additional Configuration section.  You can hard code the VID and VKEY, or use environment variables:

![AWS Code](images/5-QuickStart.png)

![AWS Code](images/6-QuickStart.png)

![AWS Code](images/7-QuickStart.png)

Start the build and it should complete successfully.

![AWS Code](images/8-QuickStart.png)

![AWS Code](images/9-QuickStart.png)


## [If you are looking to go further, see the AWS Veracode Advanced guide](/Advanced)