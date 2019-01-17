## AMI Lookup

An example of how to use a CloudFormation macro to lookup the latest AMI for a given distribution.

You'll often find the AMI reference for a distribution hardcoded directly in a CloudFormation template. Sometimes the image id property of the EC2 instance type is set; other times there will be a mapping resource between the region and the image id. In either case, the template has to be updated periodically because AMIs change over time. If you have multiple templates, this can be a bit of a challenge. The idea behind this macro is to make it easier to ensure that you are using the latest version of an AMI when launching your EC2 instances. 

It is currently a proof of concept. Let me know if you have any suggestions for improvements or additional features.

### Deploy

- Create an S3 bucket and upload the file `data.json.gz` into the bucket. The file contains the mappings between the distribution name, region, and the AMI of the latest version of the distribution. The AMIs are the ones you'll find under "Quick Start" when you launch an EC2 instance from the console. The data looks like this:
```
{
	"Amazon Linux": {
		"eu-west-1": "ami-08935252a36e25f85",
		"eu-west-2": "ami-01419b804382064e4",
		...
	},
	"Amazon Linux 2": {
		"eu-west-1": "ami-0f048a9d88ce87044",
		"eu-west-2": "ami-0aff88ed576e63e90",
		...
	}
}
```
Right now there are only mappings for Amazon Linux and Amazon Linux 2. There no entries for the AWS GovCloud region and China. 

- Create a stack with `Macro.yaml` to deploy the Lambda function and register it as a CloudFormation macro called `LookupAMI`. You'll need to enter the name of the bucket and the file. The macro can be referenced in any CloudFormation template from an EC2 instance type like so:

```yaml
EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId:
        'Fn::Transform':
          - Name: 'LookupAMI'
            Parameters:
              Name: "Amazon Linux 2"
```

- Create another stack with `VPC.yaml`. It creates a VPC with a single public subnet and launches an EC2 instance running the latest version of Amazon Linux 2.

### References

See [Accessing CloudFormation Macros from other AWS Accounts](http://www.simonbuckle.com/2019/01/15/accessing-cloudformation-macros-from-other-aws-accounts/) to learn how to grant access to the macro from other AWS accounts.