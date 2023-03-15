# Crossaccount RDS Replication

## Getting started

###  1) Visit cloudformation in AWS console of Source Acccount and deploy SourceAccountRDS.yaml template to deploy infra. 
Once deployment is successsful, please note down ***ARN of SNS topic*** From ***Resources*** tab of CloudFormation. 
   
### 2) Visit cloudformation in AWS console of Destination Acccount and deploy DestinationAccountRDS.yaml template to deploy infra. 
Once deployment is successsful, please note down ***Physical ID and ARN*** of InvokeFunction Lambda from ***Resources*** tab of CloudFormation***

### 3) Add permisson on SNS to allow subscription from lambda of destination account
#### Source Account
aws sns add-permission \
  --label lambda-access 
  --aws-account-id <destinationaccount> \
  --topic-arn arn:aws:sns:us-west-2:<sourceaccount>:CrossAccountSnapshotNotificationSNSTopic --action-name Subscribe ListSubscriptionsByTopic

### 4) Add permission on lambda to allow SNS to trigger it
#### Destination Account
aws lambda add-permission \
  --function-name HTPExp-RDSCrossAccntSync-InvokeStepFunction \
  --statement-id sns_invoke_permission \
  --action lambda:InvokeFunction \
  --principal sns.amazonaws.com \
  --source-arn arn:aws:sns:us-west-2:<sourceaccount>:CrossAccountSnapshotNotificationSNSTopic

### 5) Create a subscription
#### Destination Account
aws sns subscribe \
  --topic-arn arn:aws:sns:us-west-2:<sourceaccount>:CrossAccountSnapshotNotificationSNSTopic \
  --protocol "lambda" \
  --notification-endpoint arn:aws:lambda:us-east-1:<destinationaccount>:function:HTPExp-RDSCrossAccntSync-InvokeStepFunction


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

