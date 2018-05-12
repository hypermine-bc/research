# Creating and linking S3 bucket to a subdomain in Route 53

## Goal

Here our goal is to create a S3 bucket for subdomain and then create subdomain in Route 53 and link that subdomain with our S3 bucket.

### Pre-requisite: 
- You should have one domain created, either on AWS or Go-Daddy or any other 


### Creating S3 bucket

1. Look for S3 in services in AWS console and click on **Create Bucket** button. Provide bucket name. Give a relevant name, for example, here I have to create subdomain for **adhat.io** then I will give name something like this, **test.adhat.io**

![s301](https://github.com/hypermine-bc/research/blob/master/aws/subdom-s3-route53/s301.png)

2. Click on **Create** button. you can see your bucket in the list now.

![s302](https://github.com/hypermine-bc/research/blob/master/aws/subdom-s3-route53/s302.png)

3. Click on the bucket from the list to do some configurations...First click on **Permission** tab and then click on **Bucket Policy** button. Copy past the following json into the text area...

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::app.adhat.io/*"
        }
    ]
}
```

Change `app.adhat.io` to `test.adhat.io` and save.

![s303](https://github.com/hypermine-bc/research/blob/master/aws/subdom-s3-route53/s3003.png)

4. On **Properties** tab, click on, select `Static Web Hosting`. Choose first option, fill the details and save.
![s304](https://github.com/hypermine-bc/research/blob/master/aws/subdom-s3-route53/s305.png)

5. Upload files to bucket. Your files will be accessible through aws using this link `http://test.adhat.io.s3-website-us-east-1.amazonaws.com/` 

### Creating and linking subdomain on Route 53

1. Look for Route 53 in services in AWS console. Select your **Hosted Zone** and then press on **Create Record Set** button. Fill the form, and choose **Yes** in Alias. In the text box, select the bucket that we have previously create.

![alt text](https://github.com/hypermine-bc/research/blob/master/aws/subdom-s3-route53/route01.png)

![alt text](https://github.com/hypermine-bc/research/blob/master/aws/subdom-s3-route53/route02.png)

Now your website is acessible through your custom DNS. `http://test.adhat.io/`

