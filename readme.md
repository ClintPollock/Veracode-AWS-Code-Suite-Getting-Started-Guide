# AWS CodeSuite and Veracode

How to setup an AWS CodeSuite with Veracode Static, Software Composition Analysis, and Dynamic Analysis.

## Overview
Veracode integrates with products in the AWS CodeSuite such as CodeBuild and CodePipeline.

For this demonstration we will use the PetStoreAPI written in Python.  
https://github.com/veracode/petstore-api-flask

### AWS Products We’ll Use:

* CodeBuild - this is the primary area we integrate Veracode commands. 
* Cloud9 IDE - we'll use this to run a Docker image for the DAST scan.
* CodePipeline - integrate your security checks into your pipeline

![AWS Code](images/CheckoutArtifactScan.png)

### [Getting Started with Static and Software Composition Analysis Scanning ](1_SAST_SCA_PolicyScan)
* Create CodeBuild project
* Enter API keys in environment variables
* Paste in provided buildspec and submit the scan

### [Getting Started with Dynamic Scanning ](2_DAST)
* Use Cloud9 IDE to run the PetstoreAPI Docker Image
* Allow Veracode IP address to access Cloud9 via security group
* Submit the DAST scan

### General Flow [Advanced](Advanced)
* Create S3 bucket for artifact storage
* Create AWS CodeBuild project to submit the scan
* Pull request or component scanning with the SAST Pipeline scanner
* Create OSS Bill of Materials (SCA) for the Docker image itself
* Setup and run a DAST scan
* Using a Veracode Docker image

### More information
View more information in this four part video series using AWS Cloud9 IDE with Veracode services.

https://www.youtube.com/watch?v=P6QDC94R_bU

https://www.youtube.com/watch?v=oHw-qR9AEx8

https://www.youtube.com/watch?v=GOLoShMMUBw

https://www.youtube.com/watch?v=ndzAMaPQ_eE


Use AWS Secrets to store and deliver your API keys