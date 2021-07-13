# Setting up permissions in the AWS account
## Creating an AWS IAM policy
The exporter needs permissions to access the resources from the AWS account.

First, create an AWS IAM policy on your AWS infrastructure. The policy should allow the account to read CloudWatch metrics and get resources by tags.
An example AWS IAM configuration is given below:

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

# Monitor the resources
The YACE exporter uses an API call that filters the resources by tags.
Therefore, if you want to monitor a resource, ensure that **it has at least one tag** associated with it. A resource without a tag will not be scraped.

# Installing the exporter
To install the exporter do the following:

1. Download the `elb-deploy.yaml` file.
2. Change the following lines in the ConfigMap with the AWS region where the resources to be monitored are located:
```
region: us-east-1
```
3. Run following command and copy the content:
```
cat ~/.aws/credentials | base64
```
4. Replace the content of the `credentials` field of the secret `yace-alb-credentials` with the one that you have copied.

5. Apply the deployment:
```
kubectl apply -f elb-deploy.yaml
```
6. Ensure that the exporter is working checking that the pods are running:
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
