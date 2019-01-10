## Automatic IPv6 setup for Amazon VPC

A simple example of how to use a CloudFormation macro to modify a VPC to automatically assign an IPv6 address to EC2 instances when launched. 

### Deploy

- Create a stack with `IPv6.yaml` to deploy the Lambda function and register it as a CloudFormation macro called `EnableIPv6`. The macro can be referenced in any CloudFormation template from a top-level resource like so:

```yaml
Transform:
  - EnableIPv6
```

- Create another stack with `VPC.yaml`. It creates a VPC with a single public subnet. The macro associates an Amazon provided IPv6 CIDR block with the VPC and the subnet, and configures the subnet to automatically assign an IPv6 address to any EC2 instances.

Let me know if you have any suggestions for improvements or additional features.