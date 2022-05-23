# QuickStart Dynamic API scan

How to run a Dynamic analysis against an API endpoint

## Overview

We'll use PetStoreAPI to run our Dynamic API scan against.  We need to run the application and provide the Veracode IP address access to it.
You can use your preferred method to run Docker images.  In this case we will demonstrate using a Cloud9 IDE.

### AWS products we’ll use:

* Cloud9 IDE - a Unix based IDE with Docker support

### Veracode products we'll use

* Dynamic API scanning

### General Flow DAST Quick Start
* Create Cloud9 IDE
* Checkout code
* Build PetStore Docker image and run it
* Adjust firewall rules to allow Veracode DAST access to your site
* Test accessibility and submit the scan

Navigate to Cloud9 and create your instance.

![AWS Code](images/1-QuickStartDAST.png)


![AWS Code](images/2-QuickStartDAST.png)


![AWS Code](images/3-QuickStartDAST.png)


![AWS Code](images/4-QuickStartDAST.png)

From the terminal window, enter the following:

```bash
git clone https://github.com/ClintPollock/PetStoreAPI
cd PetStoreAPI
docker build -t petstore .

```
Next, run the Docker image.  Port 8080 on the host will be mapped to port 5000 from the Docker image.  Mapping to port 8080 allows for previewing of the app. More detail here:

https://docs.aws.amazon.com/cloud9/latest/user-guide/app-preview.html   

```bash
docker run -p 8080:5000 petstore

```

To stop the container, open another terminal window and enter:

```bash
docker container ls
docker stop CONTAINER_ID_GUID_HERE
```

Navigate to EC2 in the AWS platform.

In the left under Security Groups, create a new group.

![AWS Code](images/7-QuickStartDAST.png)


Allow port TCP port 8080 open for 34.195.146.191/32.  This is the single Veracode IP address that DAST traffic will come from. You might include your home or office IP address so you can perform basic accessibility checks.

Note the Public IP address.  You can also run this command from the Cloud9 IDE terminal to get the public IP of your Cloud9 IDE.

```bash
curl http://169.254.169.254/latest/meta-data/public-ipv4
```

![AWS Code](images/8-QuickStartDAST.png)

Apply the security group to the Cloud9 IDE instance

![AWS Code](images/9-QuickStartDAST.png)


![AWS Code](images/10-QuickStartDAST.png)


![AWS Code](images/11-QuickStartDAST.png)

You can see if the API is accessible by appending /user to the public IP address of your Cloud9 IDE.

![AWS Code](images/12-QuickStartDAST.png)

We are now ready to submit a Dynamic Scan.  You can submit DAST scans via the platform or via API connection.  You can also configure recurring DAST scans.  For our first DAST scan, lets submit it via the Veracode platform.  Details of submitting a DAST scan via API are located in the advanced section of this guide.

In the Veracode Platform, navigate to Scans & Analysis - > Dynamic Analysis.  Create a new API Specification scan.

![AWS Code](images/13-QuickStartDAST.png)

Within the source folder for PetStoreAPI you will find petstore-openapi3-spec.yml.  Edit the file, and perform a find/replace on http://localhost:5000 with http://PublicIPHere:8080

![AWS Code](images/14-QuickStartDAST.png)

Upload the updated petstore-openapi3-spec.yml and enter the custom base URL.

![AWS Code](images/15-QuickStartDAST.png)

Click the pencil icon to see available configuration options.

![AWS Code](images/16-QuickStartDAST.png)

Schedule the scan for an hour and submit.

![AWS Code](images/17-QuickStartDAST.png)

Be sure to Link the scan to an application profile so you can have SAST + SCA + DAST results in one location.

![AWS Code](images/18-QuickStartDAST.png)

DAST scanning can be done as often as you wish.  Start by setting up a recurring monthly scan and increase from there as needed.  You can also use our DAST API to submit scans from your pipeline.  See the Advanced section.

## If you are looking to go further, see the [AWS Veracode Advanced Guide](/Advanced)