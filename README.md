# Lambda access to DynamoDB via VPC Endpoint

AWS CloudFormation script that demonstrates a Lambda function running within a VPC and accessing DynamoDB using a VPC Endpoint.

The script creates a DynamoDB table, and a Lambda function that adds an item to the table.  The Lambda is associated
to a VPC that only contains private subnets (i.e. there are no Internet/NAT Gateways) and a VPC Endpoint to DynamoDB, allowing
access to the new table only.

The VPC that the Lambda function is associated with is created using the script in [VPC](https://github.com/gford1000-aws/vpc),
creating up to 6 private subnets (to which the Lambda is associated) with a CIDR of your choice.

The script creates a nested stack, constructing the VPC separately from the Lambda and DynamoDB table, for clarity.  

Notes:

1. the VPC *must* have ```EnableDnsSupport = true``` so that DNS resolution of URLs can be performed.

2. the Lambda IAM Role includes ```ec2:CreateNetworkInterface```, ```ec2:DescribeNetworkInterfaces```, ```ec2:DeleteNetworkInterface``` to allow the ENI to be created within the VPC, as well as the necessary S3 permission (s3:PutObject) to create the record in the bucket.

3. the Lambda Security Group only allows egress via the VPC EndPoint.

4. the policy of the VPC EndPoint only allows full access to the table for all principals.  This can be restricted as necessary, if required.

5. unfortunately, CloudFormation does not return the prefix list value for the VPC Endpoint service, so this must be passed to the script.  The value can be
found using the AWS CLI: [aws ec2 describe-prefix-lists](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-prefix-lists.html)

6. the IAM role used by the Lambda function allows full access to the table.  In a real-world context, this should be restricted to include only the DynamoDB actions used in the function.


## Arguments

| Argument              | Description                                                        |
| --------------------- |:------------------------------------------------------------------:|
| CidrAddress           | First 2 elements of CIDR block, which is extended to be X.Y.0.0/16 |
| PrivateSubnetCount    | The number of private subnets to be created (2-6 can be selected)  |
| DDBEndpointPrefixList | The pl-xxxxxxx identifier for the DynamoDB end point in the region |
| VPCTemplateURL        | The S3 url to the VPC Cloudformation script                        |


## Outputs

| Output                  | Description                                                    |
| ----------------------- |:--------------------------------------------------------------:|
| TableName               | The name of the DynamoDB table to which the Lambda will write  |
| Lambda                  | The name of the Lambda function                                |
| VPC                     | The reference to the VPC                                       |


## Licence

This project is released under the MIT license. See [LICENSE](LICENSE) for details.
