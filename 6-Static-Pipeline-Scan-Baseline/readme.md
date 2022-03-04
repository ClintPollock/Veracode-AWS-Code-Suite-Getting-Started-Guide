# Veracode Static Pipeline Scanner with Baseline File

The Baseline allows you to surpress Policy violating flaws the developers do not plan to fix. 

 Micro services are a great place to start with Pipeline scanner because they often have few flaws due to their small size. Not all micro services will need to use this since many micro services may not have any flaws to begin with.  The Static Pipeline scanner can be run on each PR to ensure it stays that way.

## Verify results.zip in your S3 bucket

The current configuration should save the results.json file as results.zip in the S3 bucket.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/6-Static-Pipeline-Scan-Baseline/images/1-StaticPipelineBaseline.png)

Within the Pipeline Scanner CodeBuild project, click Edit, and then Source. You can see the Edit menu allows you to fix or update details about the build project.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/6-Static-Pipeline-Scan-Baseline/images/2-StaticPipelineBaseline.png)

Click Edit - Buildspec

Under the Build:Commands section uncomment these sections:

```bash
 - unzip results.zip
```

```bash
- cp results.json Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
```

```bash
- Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).jsonWe don’t want to overwrite the current results.zip baseline file, so comment out this as well under Artifacts - 
```

```bash
-bf results.json
```

```bash
- zip results.zip results.json
```

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
      - unzip results.zip
      - java -jar /opt/veracode/pipeline-scan.jar -vid $VID -vkey $VKEY --file petstoreapi.zip -bf results.json
  post_build:
    # Here we are preparing the results to be archived.
    commands:
      - cp results.json Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
      #- zip results.zip results.json
      - ls -la
artifacts:
  files:
    - Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
    #- results.zip
```

Re-run the build and you should now see that SUCCESS: No issues passed filters. which would mean we would not break a Pull Request for example.  The AWS CodePipeline is what can help configure what CodeBuild jobs run during a Pull Request, just as running our Pipeline scanner on a pull request, and our Policy scan on a merge request.

```bash
[Container] 2022/01/06 13:13:53 Waiting for agent ping
[Container] 2022/01/06 13:13:55 Waiting for DOWNLOAD_SOURCE
[Container] 2022/01/06 13:13:57 Phase is DOWNLOAD_SOURCE
[Container] 2022/01/06 13:13:57 CODEBUILD_SRC_DIR=/codebuild/output/src965005462/src
[Container] 2022/01/06 13:13:57 YAML location is /codebuild/readonly/buildspec.yml
[Container] 2022/01/06 13:13:57 Processing environment variables
[Container] 2022/01/06 13:13:58 Moving to directory /codebuild/output/src965005462/src
[Container] 2022/01/06 13:13:58 Registering with agent
[Container] 2022/01/06 13:13:58 Phases found in YAML: 2
[Container] 2022/01/06 13:13:58  BUILD: 4 commands
[Container] 2022/01/06 13:13:58  POST_BUILD: 2 commands
[Container] 2022/01/06 13:13:58 Phase complete: DOWNLOAD_SOURCE State: SUCCEEDED
[Container] 2022/01/06 13:13:58 Phase context status code:  Message: 
[Container] 2022/01/06 13:13:58 Entering phase INSTALL
[Container] 2022/01/06 13:13:58 Phase complete: INSTALL State: SUCCEEDED
[Container] 2022/01/06 13:13:58 Phase context status code:  Message: 
[Container] 2022/01/06 13:13:58 Entering phase PRE_BUILD
[Container] 2022/01/06 13:13:58 Phase complete: PRE_BUILD State: SUCCEEDED
[Container] 2022/01/06 13:13:58 Phase context status code:  Message: 
[Container] 2022/01/06 13:13:58 Entering phase BUILD
[Container] 2022/01/06 13:13:58 Running command ls -la
total 56
drwxr-xr-x 2 root root  4096 Jan  6 13:13 .
drwxr-xr-x 3 root root  4096 Jan  6 13:13 ..
-rw-r--r-- 1 root root  7828 Jan  6 13:13 petstoreapi.zip
-rw-r--r-- 1 root root  2589 Jan  6 13:13 results.zip
-rw-r--r-- 1 root root 35159 Jan  6 13:13 Veracode-SAST-Pipeline-Results-Build-ID-39-DATE-2022-01-06.json

[Container] 2022/01/06 13:13:58 Running command ls /opt/veracode/
pipeline-scan.jar

[Container] 2022/01/06 13:13:58 Running command unzip results.zip
Archive:  results.zip
  inflating: results.json            

[Container] 2022/01/06 13:13:58 Running command java -jar /opt/veracode/pipeline-scan.jar -vid $VID -vkey $VKEY --file petstoreapi.zip -bf results.json
[06 Jan 2022 13:13:59,0078] PIPELINE-SCAN INFO: Pipeline Scan Tool Version 21.12.1-0. 
[06 Jan 2022 13:13:59,0108] PIPELINE-SCAN INFO: Beginning scanning of 'petstoreapi.zip'. 
[06 Jan 2022 13:13:59,0109] PIPELINE-SCAN INFO: Sending 7828 bytes to the server for analysis. 
[06 Jan 2022 13:14:01,0822] PIPELINE-SCAN INFO: Upload complete. 
[06 Jan 2022 13:14:02,0182] PIPELINE-SCAN INFO: Analysis Started. 
[06 Jan 2022 13:14:14,0068] PIPELINE-SCAN INFO: Analysis Complete. 
[06 Jan 2022 13:14:14,0071] PIPELINE-SCAN INFO: Analysis Results: Received 28655 bytes in 14963ms. 
[06 Jan 2022 13:14:14,0084] PIPELINE-SCAN INFO: Writing Raw JSON Results to file '/codebuild/output/src965005462/src/results.json'. 
[06 Jan 2022 13:14:14,0099] PIPELINE-SCAN INFO: Writing Filtered JSON Results to file '/codebuild/output/src965005462/src/filtered_results.json'. 

Scan Summary:
PIPELINE_SCAN_VERSION: 21.12.1-0
DEV-STAGE: DEVELOPMENT
SCAN_ID: 1f874441-140b-4229-9f7b-727af0cf5a21
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
*****************************************************************
Total flaws found: 22, New flaws found: 0 as compared to baseline
*****************************************************************

==================================
SUCCESS: No issues passed filters.
==================================


[Container] 2022/01/06 13:14:14 Phase complete: BUILD State: SUCCEEDED
[Container] 2022/01/06 13:14:14 Phase context status code:  Message: 
[Container] 2022/01/06 13:14:14 Entering phase POST_BUILD
[Container] 2022/01/06 13:14:14 Running command cp results.json Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json

[Container] 2022/01/06 13:14:14 Running command ls -la
total 132
drwxr-xr-x 2 root root  4096 Jan  6 13:14 .
drwxr-xr-x 3 root root  4096 Jan  6 13:13 ..
-rw-r--r-- 1 root root   569 Jan  6 13:14 filtered_results.json
-rw-r--r-- 1 root root  7828 Jan  6 13:13 petstoreapi.zip
-rw-r--r-- 1 root root 35159 Jan  6 13:14 results.json
-rw-r--r-- 1 root root  2589 Jan  6 13:13 results.zip
-rw-r--r-- 1 root root 35159 Jan  6 13:13 Veracode-SAST-Pipeline-Results-Build-ID-39-DATE-2022-01-06.json
-rw-r--r-- 1 root root 35159 Jan  6 13:14 Veracode-SAST-Pipeline-Results-Build-ID-40-DATE-2022-01-06.json

[Container] 2022/01/06 13:14:14 Phase complete: POST_BUILD State: SUCCEEDED
[Container] 2022/01/06 13:14:14 Phase context status code:  Message: 
[Container] 2022/01/06 13:14:14 Expanding base directory path: .
[Container] 2022/01/06 13:14:14 Assembling file list
[Container] 2022/01/06 13:14:14 Expanding .
[Container] 2022/01/06 13:14:14 Expanding file paths for base directory .
[Container] 2022/01/06 13:14:14 Assembling file list
[Container] 2022/01/06 13:14:14 Expanding Veracode-SAST-Pipeline-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).json
[Container] 2022/01/06 13:14:14 Expanded to Veracode-SAST-Pipeline-Results-Build-ID-40-DATE-2022-01-06.json
[Container] 2022/01/06 13:14:14 Found 1 file(s)
[Container] 2022/01/06 13:14:14 Phase complete: UPLOAD_ARTIFACTS State: SUCCEEDED
[Container] 2022/01/06 13:14:14 Phase context status code:  Message: 
```

If you go back to your S3 bucket you will see json files from the Pipeline scanner.  The customer could monitor these files for change in size, which would indicate a change in the data.  At this point, we don’t have a way to display information in the pull request about a Pipeline Scan, but generally, this is the direction we need to head in for all integrations in cloud CI/CD.


![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/6-Static-Pipeline-Scan-Baseline/images/3-StaticPipelineBaseline.png)

## [Continue to SCA Advanced Scan](/7-SCA-Advanced-Scan)