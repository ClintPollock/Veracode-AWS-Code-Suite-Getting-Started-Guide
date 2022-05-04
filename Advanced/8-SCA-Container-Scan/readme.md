# SCA Container Scanning

Create a new BuildProject.

![AWS Code](images/1-SCA-Agent-Container.png)

![AWS Code](images/2-SCA-Agent-Container.png)

Be sure to check the box Enable this flag to build Docker Images.

![AWS Code](images/3-SCA-Agent-Container.png)

Use buildspec:

```bash
 version: 0.2

env:
  secrets-manager:
     DOCKERHUB_PASSWORD: "dockerhub:password"
     DOCKERHUB_USERNAME: "dockerhub:username"
     SRCCLR_API_TOKEN: "veracode:SRCCLR_API_TOKEN"
phases:
  build:
    commands:
      - echo "${DOCKERHUB_PASSWORD}" | docker login -u "${DOCKERHUB_USERNAME}" --password-stdin
      - docker build -t petstore .
      - curl -sSL https://download.sourceclear.com/ci.sh | sh -s scan --image petstore > Veracode-SCA-Docker-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
      - cat Veracode-SCA-Docker-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
      - ls -la
artifacts:
  files:
    - Veracode-SCA-Docker-Results-Build-ID-$CODEBUILD_BUILD_NUMBER-DATE-$(date +%Y-%m-%d).txt
```

Set artifact location

![AWS Code](images/4-SCA-Agent-Container.png)

Before running the build, repeat the secrets permissions process, and then run the build.

![AWS Code](images/5-SCA-Agent-Container.png)

You’ll also notice the SCA Docker output has been stored in our S3 bucket.

![AWS Code](images/6-SCA-Agent-Container.png)
