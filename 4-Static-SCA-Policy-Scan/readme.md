# Submit a Veracode Static and SCA Scan

This step will download the ZIP file from the initial build CodeBuild project and submit a Veracode Static and SCA scan. 

## Create a new CodeBuild Project

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/4-Static-SCA-Policy-Scan/1-StaticSCA.png)

Configure the Source location as S3, with the folder name that contains your petstoreapi.zip

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/4-Static-SCA-Policy-Scan/2-StaticSCA.png)

This time, use this Docker image - docker.io/veracode/api-wrapper-java:latest

Be sure to enter the ARN for the docker registry credential.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/4-Static-SCA-Policy-Scan/3-StaticSCA.png)

You can customize who this works using the options with the wrapper.

The Composite Actions are the easiest -

https://docs.veracode.com/r/c_wrapper_composite_actions

You can also use any of these:

https://docs.veracode.com/r/c_wrapper_simple_actions

UploadAndScan is the primary use -

https://docs.veracode.com/r/r_uploadandscan

At this stage, it could be that they are doing 1:1 mapping of App Profiles and Micro Services, or perhaps they are combining micro services or components logically and sending up to an App Profile. It depends a bit on their licensing and CodePipeline flows.

Sample buildspec file for submitting a Static Policy Scan.

```bash
version: 0.2

env:
  secrets-manager:
      VID: "veracode:VID"
      VKEY: "veracode:VKEY"
phases:
  build:
    commands:
      - java -jar /opt/veracode/api-wrapper.jar -vid $VID -vkey $VKEY -appname AWSCodeBuild-PetStoreAPI -action UploadAndScan -createprofile true -version $CODEBUILD_BUILD_ID -filepath petstoreapi.zip
  #post_build:
    #commands:
      # - command
#artifacts:
  #files:
    # - location
```

Save the project and then give the CodeBuild project permissions to access the Secrets Manager using these instructions.

Secrets Manager Permissions:

https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/tree/main/2.1-SecretsPermissions

Then run the build and you should see a successful scan!

If you want to break the build when it does not pass Policy add -scantimeout 30

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/4-Static-SCA-Policy-Scan/4-StaticSCA.png)

In addition to breaking the CodeBuild step if there are new issues or downloading the repot, you can also login to the Veracode platform to view results.