# aws-cloudformation-playground

learn [aws cloudformation](https://aws.amazon.com/cloudformation/)

---

## Concepts

### Stacks

* Drift detection
  * [Resources that support import and drift detection operations - AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-supported-resources.html)

### [Stack Sets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html)

> enabling you to create, update, or delete stacks across multiple accounts and Regions with a single operation

* You can create a stack set with either self-managed or service-managed permissions
  * self-managed - first create the necessary IAM roles to establish a trusted relationship between the account you're administering the stack set from and the account you're deploying stack instances to
  * service-managed (use with Orgs) - deploy stack instances to accounts managed by AWS Organizations in specific Regions. With this model, you don't need to create the necessary IAM roles; StackSets creates the IAM roles on your behalf.
* automatic deployment enabled, StackSets automatically deploys to accounts that are added to the target organization or organizational units (OUs) in the future
* account gate is an optional feature that lets you specify an AWS Lambda function to verify that a target account meets certain requirements before AWS CloudFormation StackSets begins stack operations in that account

* stackset stacks should be account and region agnostic.  same template will be used for all deployments.  use SSM parameter store parameters in the target account + region for specific configurations.

---
## Running Examples

examples in [`templates/`](templates/) directory

```sh
# validate template
aws cloudformation validate-template --template-body file://templates/s3-bucket.yaml

# deploy
aws cloudformation deploy --template-file templates/s3-bucket.yaml --stack-name s3-bucket-stack

# list stack output values
aws cloudformation describe-stacks --stack-name s3-bucket-stack --query "Stacks[0].Outputs[].OutputValue"
```

### Dynamic References Example

see [`templates/dynamic-references-ssm-secrets.yaml`](templates/dynamic-references-ssm-secrets.yaml) and [Using Dynamic References to Specify Template Values
](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#dynamic-references-secretsmanager)

```sh
# validate
aws cloudformation validate-template --template-body file://templates/dynamic-references-ssm-secrets.yaml

# create stack
aws cloudformation deploy --template-file templates/dynamic-references-ssm-secrets.yaml --stack-name dynamic-references-ssm-secrets-stack

# uncomment `Outputs` in templates/dynamic-references-ssm-secrets.yaml
# update stack
aws cloudformation deploy --template-file templates/dynamic-references-ssm-secrets.yaml --stack-name dynamic-references-ssm-secrets-stack

# view stack outputs
# NOTE: `MySecret01Value` output does not get resolved due to security
aws cloudformation describe-stacks --stack-name dynamic-references-ssm-secrets-stack --query "Stacks[0].Outputs[].OutputValue"

# clean up
aws cloudformation delete-stack --stack-name dynamic-references-ssm-secrets-stack
```

![](https://www.evernote.com/l/AAFxlfEa6-9K3JXEso8e0GnGbGgM85uJC0kB/image.png)

## Resources

* [CloudFormation Custom Resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html)
* [CloudFormation Macros](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-macros.html)

---

## Scratch

```sh
aws cloudformation deploy --template-file templates/playground.yaml --stack-name playground-stack --capabilities "CAPABILITY_IAM" "CAPABILITY_NAMED_IAM" "CAPABILITY_AUTO_EXPAND"                                     

aws cloudformation describe-stacks \
  --stack-name "playground-stack" \
  --query "Stacks[0].Outputs[?OutputKey=='LambdaName'].OutputValue" --output text

playground-stack-MyLambdaFunction-iMVMRs2CA3fm

aws lambda invoke \
  --cli-binary-format "raw-in-base64-out" \
  --function-name "playground-stack-MyLambdaFunction-iMVMRs2CA3fm" \
  --payload '{"msg": "hello"}' \
  output.log; cat output.log; rm output.log

```