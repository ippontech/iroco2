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

1. Sur IroCO2, rendez-vous sur [*Intégration Cloud*](https://greensuite.ippon.fr/token) et générez une API Key pour votre compte AWS
2. Ouvrez [ce lien](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=eu-west-3#/stacks/quickcreate?templateURL=https://iroco2-cfn-templates-ppr.s3.eu-west-3.amazonaws.com/lambda.yaml&param_CURFunctionS3Bucket=iroco2-lambda-scrapper-ppr&param_CURFunctionS3Key=iroco2/scrapper/handler.zip&param_Iroco2APIEndpoint=https://api.greensuite.ippon.fr/api/scanner&param_Iroco2GatewayEndpoint=https://klmvmtymyb.execute-api.eu-west-3.amazonaws.com/test/payload-cur-part&param_LambdaLogGroupName=iroco2-log&param_LayerBucketKey=iroco2/scrapper/layers.zip&param_LayerBucketStorage=iroco2-lambda-scrapper-ppr)
3. Renseignez un nom pour
   * la stack CloudFormation (_Stack name_),
   * votre id d'organisation (_AWSOrgID_),
   * le nom du bucket S3 qui stockera vos rapports CUR (_CUROutputBucketName_)
   * l'API Key préalablement générée sur IroCO2 (_Iroco2APIKey_)
   * et l'ARN de la clé KMS (_KMSkeyARN_)
4. Validez la création de la stack Cloudformation.
5. Sur votre compte AWS, rendez-vous sur la page de *Billing and Cost Management*
6. Sur l'onglet *Data Export*, créez un export de type Legacy CUR export (le support pour CUR 2.0 arrive bientôt)
7. Cochez *Include resource IDs* et *Refresh automatically*
8. Sélectionnez la granularité sur *Daily*
9. Dans la partie *Data export storage settings*, sélectionnez le bucket créé par la stack Cloudformation
10. Saisissez le préfixe du chemin S3 (S3 path prefix) que vous voulez ajouter au nom de votre data export
11. Enfin, cliquez sur *Create report*
12. Sur le bucket S3, assurez-vous que la policy suivante est appliquée :
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
13. Attendez l'arrivée des premiers rapports CUR générés par AWS (jusqu'à 24h) pour retrouver les résultats sur le [scanner IroCO2](https://greensuite.ippon.fr/scanner)