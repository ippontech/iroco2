# Scanner carbone

## Prérequis 

- Etre connecté au compte AWS cible
- L'ARN d'une clé KMS avec la policy suivante :
```json
{
  "Version": "2012-10-17",
  "Id": "key-consolepolicy-3",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<>:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow access for Key Administrators",
      "Effect": "Allow",
      "Principal": {
        "AWS": "<>"
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
        "AWS": "<>"
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
        "AWS": "<>"
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
          "kms:EncryptionContext:aws:logs:arn": "arn:aws:logs:eu-west-3:<>:*"
        }
      }
    }
  ]
}
```

## Installation

1. Sur IroCO2, rendez vous sur *Intégration Cloud* (https://greensuite.ippon.fr/token) et générez une API Key pour votre compte AWS.

2. Ouvrez le lien suivant : https://us-east-1.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/quickcreate?templateURL=https://test-public-lambda-scanner-client-cfn.s3.eu-west-3.amazonaws.com/lambda.yaml&param_CURFunctionS3Bucket=iroco2-lambda-scrapper-ppr&param_CURFunctionS3Key=iroco2/scrapper/handler.zip&param_Iroco2APIEndpoint=https://api.greensuite.ippon.fr/api/scanner&param_Iroco2GatewayEndpoint=https://klmvmtymyb.execute-api.eu-west-3.amazonaws.com/test/payload-cur-part&param_LambdaLogGroupName=iroco2-log&param_LayerBucketKey=iroco2/scrapper/layers.zip&param_LayerBucketStorage=iroco2-lambda-scrapper-ppr

3. Renseignez votre id d'organisation (AWSOrgID), le nom du bucket qui stockera vos rapports CUR (CUROutputBucketName) et l'API Key préalablement générée sur IroCO2 (Iroco2APIKey).

4. Validez la création de la stack Cloudformation.

5. Sur votre compte AWS, rendez-vous sur la page de *Billing and Cost Management* puis sur l'onglet *Data Export*. Créez un export de type Legacy CUR export (le support pour CUR 2.0 arrive bientôt), cochez *Include resource IDs* et *Refresh automatically*. Selectionnez la granularité sur *Daily*. Dans la partie *Data export storage settings*, sélectionnez le bucket créé par la stack Cloudformation. Enfin, cliquez sur *Create report*.

6. Sur le bucket S3, assurez-vous que la policy suivante est appliquée : 
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
                "<>",
                "<>/*"
            ],
            "Condition": {
                "StringLike": {
                    "aws:SourceArn": [
                        "arn:aws:cur:us-east-1:<>:definition/*",
                        "arn:aws:bcm-data-exports:us-east-1:<>:export/*"
                    ],
                    "aws:SourceAccount": "<>"
                }
            }
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<>:root"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::<>",
                "arn:aws:s3:::<>/*"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalOrgId": "<>"
                },
                "Bool": {
                    "aws:SecureTransport": "true"
                }
            }
        },
        {
            "Effect": "Deny",
            "Principal": {
                "AWS": "<>"
            },
            "NotAction": "s3:GetObject",
            "Resource": "arn:aws:s3:::<bucket_name>/*",
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "true"
                }
            }
        }
    ]
}
```

7. Attendez l'arrivée des premiers rapports CUR générés par AWS (jusqu'à 24h) pour retrouver les résultats sur https://greensuite.ippon.fr/scanner