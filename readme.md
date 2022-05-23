# AWS CodeSuite and Veracode

How to setup an AWS CodeBuild project with Veracode Static, SCA, and Dynamic Analysis.

## Overview
A complete guide on setting leveraging Veracode services with Amazon Web Services CodeSuite.

Veracode integrates with products in the AWS CodeSuite such as CodeBuild and CodePipeline.

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
* Create S3 bucket for artifact storage
* Create AWS CodeBuild project to submit the scan
* Pull request or component scanning with the SAST Pipeline scanner
* Create OSS Bill of Materials (SCA) for the Docker image itself
* Setup and run a DAST scan