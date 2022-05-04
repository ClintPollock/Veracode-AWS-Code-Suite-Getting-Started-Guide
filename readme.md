# AWS CodeSuite and Veracode

How to setup an AWS CodeBuild project with Veracode Static and SCA Analysis.

## Overview
A complete guide on setting up an Amazon Web Services Demo Account that uses Veracode Services.

For this demonstration we will use the PetStoreAPI written in Python.  

If you plan to use your own project, we simply need to have a ZIP file passed into the Veracode Static / SCA Code build step.  If so [Proceed to Step  2](/2-SecretsSetup).

This approach uses the Veracode API Wrapper Docker image for submitting the scan.  

Veracode integrates with products in the AWS CodeSuite, specifically CodeBuild and CodePipeline.

### AWS products we’ll use:

* Cloud9 IDE (very fun and powerful IDE)
* CodeCommit - this is where we’ll place the PetStoreAPI code.
* CodeBuild - this is the primary area we integrate Veracode commands. 
* CodePipeline - pull in the CodeBuild steps to the appropriate point in your CodePipeline.


### General Flow Basic
* Create CodeCommit Repository 
* ZIP artifact and scan


### General Flow Advanced
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
* Create VC SCA  build project for the app itself
* Create VC SCA  build project for Docker Image
* Setup and run a DAST scan
