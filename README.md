# aws-cloudformation-playground

learn [aws cloudformation](https://aws.amazon.com/cloudformation/)

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

## Resources

* [CloudFormation Custom Resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html)
* [CloudFormation Macros](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-macros.html)