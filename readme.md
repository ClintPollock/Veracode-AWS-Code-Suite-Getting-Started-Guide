# AWS CodeSuite and Veracode

How to setup an AWS CodeBuild project with Veracode Static, SCA, and Dynamic Analysis.

## Overview
A complete guide on setting leveraging Veracode services with Amazon Web Services CodeSuite.

Veracode integrates with products in the AWS CodeSuite, specifically CodeBuild and CodePipeline.

For this demonstration we will use the PetStoreAPI written in Python.  

### AWS products we’ll use:

* CodeBuild - this is the primary area we integrate Veracode commands. 
* Cloud9 IDE (very fun and powerful IDE) - we'll use this to run a Docker image for the DAST scan.

### SAST + SCA General Flow [QuickStart SAST+SCA](QuickStartSAST-SCA)
* Create CodeBuild project, enter API keys in environment variables, paste buildspec, and submit.

### DAST General Flow [QuickStart DAST](QuickStartSAST-DAST)
* Use Cloud9 IDE to run the PetstoreAPI Docker Image
* Allow Veracode IP address to Cloud9 via security group
* Submit the DAST scan

### General Flow [Advanced](Advanced)
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