# Veracode API Keys and Docker Secrets configuration

Next we will setup credentials in the AWS Secrets Manager so we can use Veracode and dockerhub services. We will setup Veracode API and Key and your dockerhub username and password. If you don’t have one, create a free dockerhub account. This is needed so that you can download the docker image into AWS CodeBuild.  It may take several tries to get the CodeBuild projects right. Use the output log to troubleshoot.

## Navigate to AWS Secrets Manager

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsSetup/1-secrets.png)

Select Other type of secret, and enter three keys - VID, VKEY, and SRCCLR_API_TOKEN.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsSetup/2-secrets.png)

Name it, then keep defaults and store the secret.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsSetup/3-secrets.png)

Now we need to configure the DockerHub username. This allows us to avoid Docker Hub Rate Limiting Errors in CI/CD Pipelines. Customers may not be aware of this.  If you do not have one, create a free Docker account and enter that login detail here.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsSetup/4-secrets.png)

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsSetup/5-secrets.png)

Next, retrieve the ARN values for the Dockerhub key and store temporarily.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsSetup/6-secrets.png)

## Secrets Manager Permissions

NOTE: After each CodeBuild project is created, you must follow this process to give your CodeBuild project access to the Secrets. 

You will need to complete this step after each CodeBuild that you create which requires these credentials.

## Now we are ready to create a build project using Veracode services.

Continue to setup a Static and SCA scan