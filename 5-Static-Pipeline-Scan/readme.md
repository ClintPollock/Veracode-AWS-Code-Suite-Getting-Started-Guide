# Veracode Static Pipeline Scanner

Create a new build project for VeracodePipelineScan (name should contain no spaces). We will use the Veracode Static Pipeline Docker Image for this job. It will download the artifact and run the scan.  

More info about the Veracode Static Pipeline Scanner:
https://docs.veracode.com/r/c_about_pipeline_scan

## Create a new build project

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/images/5-Static-Pipeline-Scan/1-StaticPipeline.png)

The Source should be set to Amazon S3, enter your bucket name, and you enter the folder name. You can browse to the S3 service in the AWS console to your bucket and see the file structure.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/images/5-Static-Pipeline-Scan/2-StaticPipeline.png)

Here is where we set the environment to use our Docker image. Build tools do not exist in the Veracode Docker image, this is why we have a build and artifact step using the default image, and then use our Docker image to run the Pipeline scan. The docker image is docker.io/veracode/pipeline-scan:latest

Paste in the ARN of your dockerhub secret into Registry Credential.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/images/5-Static-Pipeline-Scan/3-StaticPipeline.png)

Click the Insert Build Commands and add modify the following three areas, or copy and paste the code from below the next three screenshots.

This is sample build spec that will run the Veracode Static Pipeline Scanner. 

```bash
version: 0.2

env:
  secrets-manager:
      VID: "veracode:VID"
      VKEY: "veracode:VKEY"
phases:
  build:
    # The LS commands simply allow you to see what is in the folder.  In the previous step we created the petstoreapi.zip.
    # It was then downloaded locally based on the Source Configuration for this project to pull from the S3 bucket."
    commands:
      - ls -la
      - ls /opt/veracode/
      #- unzip results.zip
      - java -jar /opt/veracode/pipeline-scan.jar -vid $VID -vkey $VKEY --file petstoreapi.zip
  post_build:
    # Here we are preparing the results to be archived.
    commands:
      #- cp results.json Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
      - zip results.zip results.json
      - ls -la
artifacts:
  files:
    #- Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
    - results.zip
```

Configure the Artifacts to use your S3 bucket, and enter the folder name that you see in the S3 bucket.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/images/5-Static-Pipeline-Scan/4-StaticPipeline.png)

Next, give the CodeBuild project permissions to access the Secrets Manager using these instructions.

Secrets Manager Permissions 

Then run the build.

Here you can see the sample output. 


```bash
[Container] 2022/01/06 12:27:17 Waiting for agent ping
[Container] 2022/01/06 12:27:18 Waiting for DOWNLOAD_SOURCE
[Container] 2022/01/06 12:27:20 Phase is DOWNLOAD_SOURCE
[Container] 2022/01/06 12:27:20 CODEBUILD_SRC_DIR=/codebuild/output/src298591729/src
[Container] 2022/01/06 12:27:20 YAML location is /codebuild/readonly/buildspec.yml
[Container] 2022/01/06 12:27:20 Processing environment variables
[Container] 2022/01/06 12:27:21 Moving to directory /codebuild/output/src298591729/src
[Container] 2022/01/06 12:27:21 Registering with agent
[Container] 2022/01/06 12:27:21 Phases found in YAML: 2
[Container] 2022/01/06 12:27:21  BUILD: 3 commands
[Container] 2022/01/06 12:27:21  POST_BUILD: 2 commands
[Container] 2022/01/06 12:27:21 Phase complete: DOWNLOAD_SOURCE State: SUCCEEDED
[Container] 2022/01/06 12:27:21 Phase context status code:  Message: 
[Container] 2022/01/06 12:27:21 Entering phase INSTALL
[Container] 2022/01/06 12:27:21 Phase complete: INSTALL State: SUCCEEDED
[Container] 2022/01/06 12:27:21 Phase context status code:  Message: 
[Container] 2022/01/06 12:27:21 Entering phase PRE_BUILD
[Container] 2022/01/06 12:27:21 Phase complete: PRE_BUILD State: SUCCEEDED
[Container] 2022/01/06 12:27:21 Phase context status code:  Message: 
[Container] 2022/01/06 12:27:21 Entering phase BUILD
[Container] 2022/01/06 12:27:21 Running command ls -la
total 20
drwxr-xr-x 2 root root 4096 Jan  6 12:27 .
drwxr-xr-x 3 root root 4096 Jan  6 12:27 ..
-rw-r--r-- 1 root root 7828 Jan  6 12:27 petstoreapi.zip
-rw-r--r-- 1 root root 2588 Jan  6 12:27 results.zip

[Container] 2022/01/06 12:27:21 Running command ls /opt/veracode/
pipeline-scan.jar

[Container] 2022/01/06 12:27:21 Running command java -jar /opt/veracode/pipeline-scan.jar -vid $VID -vkey $VKEY --file petstoreapi.zip
WARNING: sun.reflect.Reflection.getCallerClass is not supported. This will impact performance.
[06 Jan 2022 12:27:21,0983] PIPELINE-SCAN INFO: Pipeline Scan Tool Version 21.12.1-0. 
[06 Jan 2022 12:27:21,0989] PIPELINE-SCAN INFO: Beginning scanning of 'petstoreapi.zip'. 
[06 Jan 2022 12:27:21,0990] PIPELINE-SCAN INFO: Sending 7828 bytes to the server for analysis. 
[06 Jan 2022 12:27:24,0761] PIPELINE-SCAN INFO: Upload complete. 
[06 Jan 2022 12:27:25,0159] PIPELINE-SCAN INFO: Analysis Started. 
[06 Jan 2022 12:27:37,0093] PIPELINE-SCAN INFO: Analysis Complete. 
[06 Jan 2022 12:27:37,0096] PIPELINE-SCAN INFO: Analysis Results: Received 28655 bytes in 15107ms. 
[06 Jan 2022 12:27:37,0115] PIPELINE-SCAN INFO: Writing Raw JSON Results to file '/codebuild/output/src298591729/src/results.json'. 
[06 Jan 2022 12:27:37,0132] PIPELINE-SCAN INFO: Writing Filtered JSON Results to file '/codebuild/output/src298591729/src/filtered_results.json'. 

Scan Summary:
PIPELINE_SCAN_VERSION: 21.12.1-0
DEV-STAGE: DEVELOPMENT
SCAN_ID: 7caed112-5f48-4f11-bf74-97d0483201be
SCAN_STATUS: SUCCESS
SCAN_MESSAGE: Scan successful. Results size: 28156 bytes
====================
Analysis Successful.
====================

===================
Analyzed 1 modules.
===================
Python files within petstoreapi.zip

===================
Analyzed 22 issues.
===================
-------------------------------------
Found 1 issues of Very High severity.
-------------------------------------
CWE-78: Improper Neutralization of Special Elements used in an OS Command ('OS Command Injection'): api.py:359
---------------------------------
Found 19 issues of High severity.
---------------------------------
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:48
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:55
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:71
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:105
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:108
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:136
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:153
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:156
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:184
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:198
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:214
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:217
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:236
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:260
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:263
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:290
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:305
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:321
CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection'): api.py:324
----------------------------------
Found 2 issues of Medium severity.
----------------------------------
CWE-489: Leftover Debug Code: api.py:478
CWE-73: External Control of File Name or Path: api.py:370

=========================
FAILURE: Found 22 issues!
=========================


[Container] 2022/01/06 12:27:37 Command did not exit successfully java -jar /opt/veracode/pipeline-scan.jar -vid $VID -vkey $VKEY --file petstoreapi.zip exit status 22
[Container] 2022/01/06 12:27:37 Phase complete: BUILD State: FAILED
[Container] 2022/01/06 12:27:37 Phase context status code: COMMAND_EXECUTION_ERROR Message: Error while executing command: java -jar /opt/veracode/pipeline-scan.jar -vid $VID -vkey $VKEY --file petstoreapi.zip. Reason: exit status 22
[Container] 2022/01/06 12:27:37 Entering phase POST_BUILD
[Container] 2022/01/06 12:27:37 Running command zip results.zip results.json
updating: results.json (deflated 93%)

[Container] 2022/01/06 12:27:37 Running command ls -la
total 92
drwxr-xr-x 2 root root  4096 Jan  6 12:27 .
drwxr-xr-x 3 root root  4096 Jan  6 12:27 ..
-rw-r--r-- 1 root root 35159 Jan  6 12:27 filtered_results.json
-rw-r--r-- 1 root root  7828 Jan  6 12:27 petstoreapi.zip
-rw-r--r-- 1 root root 35159 Jan  6 12:27 results.json
-rw-r--r-- 1 root root  2589 Jan  6 12:27 results.zip

[Container] 2022/01/06 12:27:37 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2022/01/06 12:27:37 Phase context status code:  Message: 
[Container] 2022/01/06 12:27:37 Expanding base directory path: .
[Container] 2022/01/06 12:27:37 Assembling file list
[Container] 2022/01/06 12:27:37 Expanding .
[Container] 2022/01/06 12:27:37 Expanding file paths for base directory .
[Container] 2022/01/06 12:27:37 Assembling file list
[Container] 2022/01/06 12:27:37 Expanding results.zip
[Container] 2022/01/06 12:27:37 Found 1 file(s)
[Container] 2022/01/06 12:27:37 Phase complete: UPLOAD_ARTIFACTS State: SUCCEEDED
[Container] 2022/01/06 12:27:37 Phase context status code:  Message: 
```

Navigate to the S3 bucket and you will now see there is another artifact containing the zipped up json output of the Pipeline scanner.  It will be up to the Dev team to architect this into their existing process.

Now lets setup the Pipeline Scanner to use the Baseline file. Of course we would never want something to ship that has the kind of vulns that PetStoreAPI has, and they should be fixed. The baseline file is only needed if the developers have Policy violating flaws that they do not plan to fix. 

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/images/5-Static-Pipeline-Scan/5-StaticPipeline.png)


## [Static Pipeline Scanner with Baseline File](/6-Static-Pipeline-Scan-Baseline)
