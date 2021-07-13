# Enabling requests metrics for S3 Buckets
If you want to get the CloudWatch request metrics for the objects in a bucket, you must create a metrics configuration for the bucket.
When enabled, request metrics are reported for all object operations.

# Setting up permissions in the AWS account
## Creating an AWS IAM policy
The exporter needs permissions to access the resources from the AWS account.

First, create an AWS IAM policy on your AWS infrastructure. The policy should allow the account to read CloudWatch metrics and get resources by tags.
An example AWS IAM configuration:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchExporterPolicy",
            "Effect": "Allow",
            "Action": [
                "tag:GetResources",
                "cloudwatch:ListTagsForResource",
                "cloudwatch:GetMetricStatistics",
                "cloudwatch:GetMetricData",
                "cloudwatch:ListMetrics"
            ],
            "Resource": "*"
        }
    ]
}
```

## Creating the credentials for the exporter
Create a `$HOME/.aws/credentials` file as follows. Substitute the values with your key and password:

```ini
# CREDENTIALS FOR AWS ACCOUNT
aws_region = us-east-1
aws_access_key_id = AYYYYYZZZZZZ3BLXXXXX
aws_secret_access_key = bXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

# Configuring the resources to monitor
The YACE exporter uses an API call that filters the resources by tags.
Therefore, if you want to monitor a resource, ensure that **it has at least one tag** associated with it. A resource without a tag will not be scraped.

# Installing the exporter
This setup creates two instances of the yace exporter:
- One for daily metrics with scraping interval of 2 hours
- Another for requests metrics with standard scraping interval

To install the exporter follow this steps:

1. Download the `s3-deploy.yaml` file.
2. Change the following lines in the ConfigMap with the AWS region where the resources to monitor are located:
```
region: us-east-1
```
3. Run following command and copy the content:
```
cat ~/.aws/credentials | base64
```
3. Replace the content of the `credentials` field of the secret `yace-s3-credentials` with the one that you have copied.
4. Uncomment the metrics not used in the ConfigMap to make them available.
5. The daily metrics are refreshed by the exporter every 2 hours. To change it, modify the following line in the deployment:
```
- "--scraping-interval=7200"
```
6. Apply the deployment:
```
kubectl apply -f s3-deploy.yaml
```
7. Ensure that the exporter is working checking that the pods are running:
```
kubectl -n yace get pods
```

# Sysdig Agent configuration
Do the following:

1. In the yace deployment, include the Prometheus annotations. Add the port of the exporter as the scraping port in the annotation.    

2. Download the sample [Sysdig Agent configuration file](include/sysdig-agent-config.yaml) and apply it by:
```bash
kubectl apply -f sysdig-agent-config.yaml
```
