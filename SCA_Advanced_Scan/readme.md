# SCA Advanced Scanning

![AWS Code](images/1-SCA-Agent.png)

In the case of SCA Agent we are going to download the source raw on an image with Python.  If this were Java with Maven, Maven would need to be installed on the image.

![AWS Code](images/2-SCA-Agent.png)

![AWS Code](images/3-SCA-Agent.png)

Use this for the buildspec.

```bash
 version: 0.2

env:
  secrets-manager:
     SRCCLR_API_TOKEN: "veracode:SRCCLR_API_TOKEN"
phases:
  build:
    commands:
      - curl -sSL https://download.sourceclear.com/ci.sh | sh >> Veracode-SCA-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
      - cat Veracode-SCA-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
  post_build:
    commands:
       - ls -la
artifacts:
  files:
    - Veracode-SCA-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
```

Set the Artifacts location so we can save SCA results to the S3 bucket.

![AWS Code](images/4-SCA-Agent.png)

Save project, then go into IAM - Roles and click on the role with the name similar to the CodeBuild project you created.

![AWS Code](images/5-SCA-Agent.png)


Attach Polices, search for SecretManagerReadWrite.

![AWS Code](images/6-SCA-Agent.png)

Run the build and you should see the SCA results!

![AWS Code](images/7-SCA-Agent.png)

We’ve also uploaded the results to the S3 bucket.

![AWS Code](images/8-SCA-Agent.png)

## [Continue to SCA Container Scan](/SCA-Container-Scan)