# Carbon Scanner

## Prerequisites

- Be connected to the target AWS account
- The ARN of a KMS key with the following policy:
```json
{
  "Version": "2012-10-17",
  "Id": "key-consolepolicy-3",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<Account_ID>:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow access for Key Administrators",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<Account_ID>:role/<Role_Name>"
      },
      "Action": [
        "kms:Create*",
        "kms:Describe*",
        "kms:Enable*",
        "kms:List*",
        "kms:Put*",
        "kms:Update*",
        "kms:Revoke*",
        "kms:Disable*",
        "kms:Get*",
        "kms:Delete*",
        "kms:TagResource",
        "kms:UntagResource",
        "kms:ScheduleKeyDeletion",
        "kms:CancelKeyDeletion",
        "kms:RotateKeyOnDemand"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<Account_ID>:role/<Role_Name>"
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<Account_ID>:role/<Role_Name>"
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    },
    {
      "Sid": "Enable CloudWatch Logs Access",
      "Effect": "Allow",
      "Principal": {
        "Service": "logs.eu-west-3.amazonaws.com"
      },
      "Action": [
        "kms:Encrypt*",
        "kms:Decrypt*",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:Describe*"
      ],
      "Resource": "*",
      "Condition": {
        "ArnLike": {
          "kms:EncryptionContext:aws:logs:arn": "arn:aws:logs:eu-west-3:<Account_ID>:*"
        }
      }
    }
  ]
}
```

## Installation

1. On IroCO2, go to [*Integrate Cloud*](https://greensuite.ippon.fr/token) and generate an API Key for your AWS account
2. Open [this link](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/quickcreate?templateURL=https://iroco2-cfn-templates-ppr.s3.eu-west-3.amazonaws.com/lambda.yaml&param_CURFunctionS3Bucket=iroco2-lambda-scrapper-ppr&param_CURFunctionS3Key=iroco2/scrapper/handler.zip&param_Iroco2APIEndpoint=https://api.greensuite.ippon.fr/api/scanner&param_Iroco2GatewayEndpoint=https://klmvmtymyb.execute-api.eu-west-3.amazonaws.com/test/payload-cur-part&param_LambdaLogGroupName=iroco2-log&param_LayerBucketKey=iroco2/scrapper/layers.zip&param_LayerBucketStorage=iroco2-lambda-scrapper-ppr)
3. Fill in a name for
   * the CloudFormation stack (_Stack name_),
   * your organization ID (_AWSOrgID_),
   * the name of the S3 bucket that will store your CUR reports (_CUROutputBucketName_),
   * the API Key previously generated on IroCO2 (_Iroco2APIKey_),
   * and the ARN of the KMS key (_KMSkeyARN_)
4. Validate the creation of the Cloudformation stack.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EnableAWSDataExportsToWriteToS3AndCheckPolicy",
            "Effect": "Allow",
            "Principal": {
                "Service": [
                    "billingreports.amazonaws.com",
                    "bcm-data-exports.amazonaws.com"
                ]
            },
            "Action": [
                "s3:PutObject",
                "s3:GetBucketPolicy"
            ],
            "Resource": [
                "arn:aws:s3:::<Bucket_Name>",
                "arn:aws:s3:::<Bucket_Name>/*"
            ],
            "Condition": {
                "StringLike": {
                    "aws:SourceArn": [
                        "arn:aws:cur:us-east-1:<Account_ID>:definition/*",
                        "arn:aws:bcm-data-exports:us-east-1:<Account_ID>:export/*"
                    ],
                    "aws:SourceAccount": "<Account_ID>"
                }
            }
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<Account_ID>:root"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::<Bucket_Name>",
                "arn:aws:s3:::<Bucket_Name>/*"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalOrgId": "<Organization_ID>"
                },
                "Bool": {
                    "aws:SecureTransport": "true"
                }
            }
        },
        {
            "Effect": "Deny",
            "Principal": {
                "AWS": "arn:aws:iam::<Account_ID>:role/<Lambda_Scanner_Execution_Role_Name>"
            },
            "NotAction": "s3:GetObject",
            "Resource": "arn:aws:s3:::<Bucket_Name>/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "true"
                }
            }
        }
    ]
}
```
13. Wait for the first CUR reports generated by AWS (up to 24 hours) to find the results on the [IroCO2 scanner](https://greensuite.ippon.fr/scanner)