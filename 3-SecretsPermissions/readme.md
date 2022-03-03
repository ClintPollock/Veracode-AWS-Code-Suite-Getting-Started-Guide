# Secret Manager Permissions

Each new CodeBuild project must get permissions to access the secrets we created, otherwise you might get this error -

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsPermissions/7-secrets.png)

To fix, we must go into the IAM service, Identity and Access Management.

Click on Roles, and then you will see your Pipeline scanner role, in this case -

codebuild-VeracodePipelineScan-service-role

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsPermissions/8-secrets.png)

Click on the role, and then Attach Permissions. Search for SecretsManager, and check the box, then Attach the Policy.

![AWS Code](https://github.com/ClintPollock/AWS-Code-Suite-Veracode-Examples/raw/main/2-SecretsPermissions/9-secrets.png)

## Now we are ready to submit your CodeBuild Project.