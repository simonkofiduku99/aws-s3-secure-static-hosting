# Project 4: S3 Data Security & Secure Static Hosting - MU Beauty Salon & Spa

> Secure, production-grade static website hosting using S3 private bucket + CloudFront OAC + ACM HTTPS.

Live Site: `https://d38pyfnt1r1nuf.cloudfront.net`
S3 Bucket: `mu-beauty-salon-and-spa`

### Architecture
User (HTTPS) -> CloudFront (ACM cert, OAC) -> Private S3 Bucket (Block Public Access ON)
                            |
                            -> S3 Access Logs Bucket (mu-beauty-logs)

### Goal
Demonstrate data-at-rest security and public-exposure prevention - a commonly tested skill in AWS interviews.

### What I Built
- **S3 Security:** Block Public Access = ON (all 4 checks), Bucket Versioning Enabled, Default Encryption SSE-S3 (AES-256) enabled
- **Least-Privilege Access:** Bucket policy allows ONLY CloudFront Origin Access Control (OAC) with `AWS:SourceArn` condition. No `Principal: *`.
- **Secure Delivery:** CloudFront distribution fronting S3 with Viewer Protocol Policy: `Redirect HTTP to HTTPS`, Origin configured with OAC (not public website endpoint)
- **Encryption in Transit:** ACM certificate (us-east-1) for CloudFront, TLS 1.2+ enforced
- **Auditing:** Server Access Logging enabled to separate log bucket `mu-beauty-logs` with prefix `access-logs/`

### Bucket Policy (Least-Privilege)
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "AllowCloudFrontOAC",
    "Effect": "Allow",
    "Principal": { "Service": "cloudfront.amazonaws.com" },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::mu-beauty-salon-and-spa/*",
    "Condition": {
      "StringEquals": {
        "AWS:SourceArn": "arn:aws:cloudfront::ACCOUNT_ID:distribution/DISTRIBUTION_ID"
      }
    }
  }]
}
