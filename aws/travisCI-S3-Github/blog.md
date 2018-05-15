# Continous integration of Github repo with S3 bucket using TravisCI

## Overview

Continous integration might seem to be very complicated at first, especially when it comes to coders, but don't intimidate we will try to understand the concept of CI as simple as we can and then will try to implement continuous deployment of a Git repo on AWS S3 bucket using TravisCI.

To understand CI, imagine a scenario where your team is working on a project, committing changes to version control, and every time they commit a change, you want the code to be built, tested and deployed on S3. One way can be, you build the code locally, test it and then upload all static files in S3 manually or using awscli to accomplish the deployment task. But then doing all these manually is a bit overhead right? You have to do all these steps, every time a new commit occurs. 

And this is where continuous integration comes into the picture. 

`Its a process of building, testing and deploying the code automatically every time team member checks into version control`

In this article, we will see how we can integrate TravisCI with our project in Github to AWS S3. We will use following components: 

1. **Version Control** : Git
2. **Project Type** : Node JS
3. **CI tool** : TravisCI
4. **Cloud Object Storage** : AWS S3 

There are many CI tools available, but we will be using TravisCI because its free for all open source projects hosted on the GitHub and for the first 100 builds otherwise. You can find the list of available CI tools [here](https://code-maze.com/top-8-continuous-integration-tools/)

## Steps to follow

1. Setting up Git Repo
2. Setting up Travis account
3. AWS Setup
    * Setting up S3
    * Creating IAM user for TravisCI
    * Test the IAM user has access to S3 bucket or not. (Optional)
        * Installing and using AWS CLI
        * Uploading files to S3 using aws-cli through IAM user
4. Configuring .travis.yml file in repo 

## Setting up Git Repo

Create a git repository and push your node js project code to it.

## Setting up Travis account

Go to this link https://travis-ci.com/ and signup with GitHub account. Provide Github username and password -> Authorize to Travis -> Press on Activate button -> Choose either all repos or selected a repo -> Press Approve and Install. 

Once you are in the home page, on your desired repository, press on setting button -> Enable Build only if .travis.yml file present.

## AWS Setup

### Setting up S3

1. Login to AWS console and create an S3 bucket.
2. Once the bucket is created, go to Properties tab and enable `Static website hosting`

### Creating IAM user for TravisCI

1. Go to AWS console and search for IAM -> add a new IAM user
2. Give `Programmatic Access`
3. Copy `Secret Access Key` and `Access Key ID`, we are going to need it later.
4. Once the user is created, click on them and on the `Permission` tab, hit `Add Inline permission` link.
5. Open the JSON tab and past the following JSON. Make sure you replace the resources by your bucket name.  
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "arn:aws:s3:::*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::your_bucket_name"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:DeleteObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::your_bucket_name/*"
            ]
        }
    ]
}
```
6. Click on create policy button, give a proper name and save. 

## Test the IAM user has access to S3 bucket or not
This step is optional, you may skip this step.

### Installing and using AWS CLI
Let’s use AWS cli to login with our new user and uploading files to S3… This is for Ubuntu. 

1. Now that you have installed python, use the command to install awscli `sudo apt install awscli`
2. Verify aws is installed, `aws --version`
3. Configure aws with a newly created user.
    * Type command `aws configure`
    * Provide Access Key ID, Secret Access Key that you have copied earlier.
    * Provide `Default region name` as us-east-1 (depending upon your configuration) and `Default output` as JSON.
    
    
**Note**: If some error occurs while installing awscli, please install python first.

### Uploading files to S3 using aws-cli through IAM user

1. Create index.html file in a folder and cd to into it. 
2. Use command  `aws s3 cp index.html s3://[bucket_name]/index.html` to copy the file to s3 bucket. Don’t forget to replace the bucket name in the command. 

## Configuring .travis.yml file in repo

In TravisCI, builds are configured using .travis.yml file which contains the build tasks that will be executed on running the build. 

Clone the existing repo and create .travis.yml file in root folder.
Add following code into it.

```

language: node_js
node_js:
 - node
cache:
 directories:
   - node_modules
deploy:
 provider: s3
 access_key_id: "YOUR_ACCESS_KEY"
 secret_access_key: "YOUR_SECRET_KEY"
 bucket: "YOUR_S3_BUCKET"
 acl: public_read
 skip_cleanup: true
 local_dir: build_webpack
branches:
 only:
   - master
addons:
   apt:
     update: true
```

1. This config file is telling Travis to build the project as node project. Hence the language is specified as node_js
2. Replace “YOUR_ACCESS_KEY” and “YOUR_SECRET_KEY” with keys that you have copied earlier.
3. Change the **bucket** field as well.
4. **local_dir**  field tells Travis to deploy only the specified directory (here build_webpack) to the bucket so that you won’t have to deploy your whole code.
5. **apt -> update** true : property tells to run apt-get update.
6. **acl** property can set Travis correct file permission. In this case it's public. To read more here.

Now you can do changes in any file, say index.html. Build your project using command, say `npm run build` then commit the changes. Travis will detect the new commit and trigger the build for you. 

You might have noted that we are providing Access Key and Secret Key in .travis.yml and then checking this file along with other changes. Doing this will reveal your key so it's better to encrypt these keys first and then add them to .travis.yml file. You can accomplish this task in either of these two ways.


[Download](https://github.com/travis-ci/travis.rb) travis client -> Open terminal -> type command `travis encrypt --add deploy.secret_access_key secret_access_key="YOUR_SECRET_KEY"`

                                                         OR
(Easiest way)
Open TravisCI web app ->  login -> click on `setting` button near your repo -> look for environment variables section -> Add YOUR_SECRET_KEY, YOUR_ACCESS_KEY and YOUR_S3_BUCKET as variables and specify their proper values as well. Now, you can refer to environment variables from .travis.yml like this :

```
....
 access_key_id: "$YOUR_ACCESS_KEY"
 secret_access_key: "$YOUR_SECRET_KEY"
 bucket: "$YOUR_S3_BUCKET"
....

```

Hope this article will help you in CI next time. For more, please refer to TravisCI [doc](https://docs.travis-ci.com/user/deployment/s3/) 












 





