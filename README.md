# CloudFront + Private S3 Bucket Setup

## Steps Followed

1. **Created an S3 Bucket**
   - Bucket name: `student-private-bucket-favour`
   - Uploaded objects: `favour.html`, `error.html`, `file-standard.txt`
   - Ensured **Block Public Access** settings were enabled

2. **Created a CloudFront Distribution**
   - Origin domain: `student-private-bucket-favour.s3.eu-north-1.amazonaws.com` (bucket endpoint)
   - Enabled **Origin Access Control (OAC)** for private bucket access
   - Attached the OAC to the CloudFront origin
 
3. **Updated S3 Bucket Policy**
   - Allowed CloudFront to access the bucket using the distribution ARN
   - Sample policy used:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "AllowCloudFrontAccessViaOAC",
         "Effect": "Allow",
         "Principal": { "Service": "cloudfront.amazonaws.com" },
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::student-private-bucket-favour/*",
         "Condition": {
           "StringEquals": {
             "AWS:SourceArn": "arn:aws:cloudfront::128031758934:distribution/E15DCVDC1E2UHA"
           }
         }
       }
     ]
   }

4. **Tested Access**

-Attempted direct S3 access → Access Denied

-Access via CloudFront URL → File loaded successfully


## Why CloudFront + Private S3 is Better than Public S3

-Security: **The S3 bucket remains private; only CloudFront can read objects.**

-Performance: **CloudFront caches content at edge locations for faster delivery globally.**

-Cost Optimization: **Reduces S3 GET requests and bandwidth usage by serving cached content.**

-Custom Rules: **CloudFront allows cache behavior, HTTPS enforcement, and signed URLs for fine-grained control.**

-Scalability: **Handles high traffic spikes better than serving directly from S3.**


##Issues Faced and Solutions

**AccessDenied Errors**

-Occurred when using the S3 static website endpoint with a private bucket (http://student-private-bucket-favour.s3-website.eu-north-1.amazonaws.com)

-Solution: Used the bucket endpoint (student-private-bucket-favour.s3.eu-north-1.amazonaws.com) and attached an OAC to CloudFront.
