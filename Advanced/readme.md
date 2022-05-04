# AWS CodeSuite and Veracode Advanced

How to setup an AWS CodeBuild project with additional capability.

## Overview
A complete guide on setting up an Amazon Web Services Demo Account that uses Veracode Services.

For this demonstration we will use the PetStoreAPI written in Python.  

If you plan to use your own project, we simply need to have a ZIP file passed into the Veracode Static / SCA Code build step.  If so [Proceed to Step  2](2-SecretsSetup).

This approach uses the Veracode API Wrapper Docker image for submitting the scan.  

Veracode integrates with products in the AWS CodeSuite, specifically CodeBuild and CodePipeline.

Start with the basic approach to get a scan working and then review the advanced options to understand all capability.
The basic requirement for governance purposes is a consistent Policy Static+SCA scan.  If there are multiple web services we suggest you bundle them up into a ZIP file and submit them to a Veracode Application profile for tracking.

From there, you might leverage some of the options in this advanced approach.

### AWS products we’ll use:

* Cloud9 IDE (very fun and powerful IDE)
* CodeCommit - this is where we’ll place the PetStoreAPI code.
* CodeBuild - this is the primary area we integrate Veracode commands. 
* CodePipeline - pull in the CodeBuild steps to the appropriate point in your CodePipeline.

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
* Create VC SCA  build project for the app itself
* Create VC SCA  build project for Docker Image
* Setup and run a DAST scan

## Steps

#### [Step 1 Build Project and create ZIP artifact](1-InitialSetup)
#### [Step 2 Setup Veracode and DockerHub API Keys in AWS Secrets](2-SecretsSetup)
#### [Step 3 Setting permissions for CodeBuild projects to access AWS Secrets](3-SecretsPermissions)
#### [Step 4 Static Policy and SCA Scan](4-Static-SCA-Policy-Scan)
#### [Step 5 Static Pipeline Scan](-Static-Pipeline-Scan)
#### [Step 6 Static Pipeline Scan with Baseline File](6-Static-Pipeline-Scan-Baseline)
#### [Step 7 SCA Advanced Scan](7-SCA-Advanced-Scan)
#### [Step 8 SCA Container Scan](8-SCA-Container-Scan)
#### [Step 9 Dynamic Scan](9-DAST-Scan)
