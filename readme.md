# AWS CodeSuite and Veracode

How to setup an AWS CodeSuite with Veracode Static Analysis, Software Composition Analysis, and Dynamic Analysis.

## Overview
Veracode integrates with products in the AWS CodeSuite such as CodeBuild and CodePipeline.

For this demonstration we will use the PetStoreAPI written in Python.  
https://github.com/veracode/petstore-api-flask

### AWS Products We’ll Use:

* CodeBuild - this is the primary area we integrate Veracode commands. 
* Cloud9 IDE - we'll use this to run a Docker image for the DAST scan.
* CodePipeline - integrate your security checks into your pipeline

The basic flow we'll be demonstrating is Checkout - Artifact - Scan.

![AWS Code](Checkout_CreateArtifact_Scan.png)

### [Static Analysis and Software Composition Analysis Scanning](SAST_SCA_Scan/)
* Create CodeBuild project
* Enter API keys in environment variables
* Paste in provided buildspec example to which will Checkout, Artifact, and Scan within a single buildspec file
* Submit the build to get SAST/SCA scan results

### [Dynamic Scanning](DAST_Scan/)
* Use Cloud9 IDE to run the PetstoreAPI Docker Image
* Allow Veracode IP address to access Cloud9 via security group
* Submit the DAST scan

### [SCA Advanced Scanning](SCA_Advanced_Scan/)
* Vulnerable method detection
* [Container scanning](SCA_Container_Scan/)
* Create Open Source Software Bill of Materials (SCA) for the container image itself.

### [Break on a Pull Request](SAST_Pipeline_Scanner)
* Use the Veracode Static Pipeline scanner for quick feedback on a pull or merge request.