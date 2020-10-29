# Intrinsic Functions



### Base64

Returns the Base64 representation of the input string. This function is typically used to pass encoded data to Amazon EC2 instances by way of the UserData property. 

```yaml
Fn::Base64: AWS CloudFormation
```

**Can be used with !Sub to allow CF values**

```yaml
UserData:
  Fn::Base64: !Sub |
     #!/bin/bash -xe
     yum update -y aws-cfn-bootstrap
     /opt/aws/bin/cfn-init -v --stack ${AWS::StackName} --resource LaunchConfig --region ${AWS::Region}
     /opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackName} --resource WebServerGroup --region ${AWS::Region}
```


###  FindInMap

The intrinsic function Fn::FindInMap returns the value corresponding to keys in a two-level map that is declared in the Mappings section. 

```yaml
Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]
```

```yaml
Mappings: 
  RegionMap: 
    us-east-1: 
      HVM64: "ami-0ff8a91507f77f867"
      HVMG2: "ami-0a584ac55a7631c0c"
    us-west-1: 
      HVM64: "ami-0bdb828fd58c52235"
      HVMG2: "ami-066ee5fd4a9ef77f1"
    eu-west-1: 
      HVM64: "ami-047bb4163c506cd98"
      HVMG2: "ami-31c2f645"
    ap-southeast-1: 
      HVM64: "ami-08569b978cc4dfa10"
      HVMG2: "ami-0be9df32ae9f92309"
    ap-northeast-1: 
      HVM64: "ami-06cd52961ce9f0d85"
      HVMG2: "ami-053cdd503598e4a9d"
Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap
        - RegionMap
        - !Ref 'AWS::Region'
        - HVM64
      InstanceType: m1.small
```


### Cidr

Returns an array of CIDR address blocks. The number of CIDR blocks returned is dependent on the count parameter. 

```yaml
Fn::Cidr: 
  - ipBlock 
  - count
  - cidrBits
```



### GetAtt

Returns the value of an attribute from a resource in the template.  Limited to what is available per resource, each resource definition had details of what is available.

```yaml
Fn::GetAtt: [ logicalNameOfResource, attributeName ]
```

```yaml
!GetAtt myELB.DNSName
```


### GetAZs

Returns an array that lists Availability Zones for a specified region in alaphabetical order.

```yaml
AvailabilityZone: !Select
  - 0
  - Fn::GetAZs: !Ref 'AWS::Region'
```

```yaml
AvailabilityZone: !Select
  - 0
  - !GetAZs
    Ref: 'AWS::Region'
```


### ImportValue

Returns the value of an output exported by another stack. You typically use this function to create cross-stack references. You can't use the short form of !ImportValue when it contains a !Sub. 

```yaml
Fn::ImportValue:
  !Sub "${NetworkStackName}-SecurityGroupID"
```


### Join

Appends a set of values into a single value, separated by the specified delimiter. If a delimiter is the empty string, the set of values are concatenated with no delimiter. 

```yaml
!Join [ " ", [ a, b, c ] ]
```
returns: "a b c"

```yaml
!Join
  - ''
  - - 'arn:'
    - !Ref AWS::Partition
    - ':s3:::elasticbeanstalk-*-'
    - !Ref 'AWS::AccountId'
```


### Select

Returns a single object from a list of objects by index. 

```yaml
Subnet0:
  Type: "AWS::EC2::Subnet"
  Properties:
    VpcId: !Ref VPC
    CidrBlock: !Select [ 0, !Ref DbSubnetIpBlocks ]
```


### Split

Split a string into a list of string values so that you can select an element from the resulting string list.  Use !Select to pick one.

```yaml
!Split [ "|" , "a|b|c" ]
```

```yaml
!Select [2, !Split [",", !ImportValue AccountSubnetIDs]]
```


### Sub

Substitutes variables in an input string with values that you specify. In your templates, you can use this function to construct commands or outputs that include values that aren't available until you create or update a stack.

```yaml
!Sub 'arn:aws:ec2:${AWS::Region}:${AWS::AccountId}:vpc/${vpc}'
```


### Transform

Specifies a macro to perform custom processing on part of a stack template. Macros enable you to perform custom processing on templates, from simple actions like find-and-replace operations to extensive transformations of entire templates

```yaml
'Fn::Transform':
    Name: 'AWS::Include'
    Parameters: {Location: {'Fn::FindInMap': [RegionMap, us-east-1, s3Location]}}
```

### Ref

Returns the value of the specified parameter or resource. 

```yaml
MyEIP:
  Type: "AWS::EC2::EIP"
  Properties:
    InstanceId: !Ref MyEC2Instance
```



### Pseudo parameters

Pseudo parameters are parameters that are predefined by AWS CloudFormation. You do not declare them in your template. Use them the same way as you would a parameter, as the argument for the Ref function.


### AWS::Region

```yaml
Outputs:
  MyStacksRegion:
    Value: !Ref "AWS::Region"
```

### AWS::AccountId

### AWS::NoValue

### AWS::Partition

Returns the partition that the resource is in. For standard AWS regions, the partition is aws. For resources in other partitions, the partition is aws-partitionname. For example, the partition for resources in the China (Beijing and Ningxia) region is aws-cn and the partition for resources in the AWS GovCloud (US-West) region is aws-us-gov. 

### AWS::StackId

### AWS::StackName

### AWS::URLSuffix

Returns the suffix for a domain. The suffix is typically amazonaws.com, but might differ by region. For example, the suffix for the China (Beijing) region is amazonaws.com.cn.


### Condition Functions

Condition functions are intrinsic functions

```yaml
AWSTemplateFormatVersion: "2010-09-09"

Mappings:
  RegionMap:
    us-east-1:
      AMI: "ami-0ff8a91507f77f867"
    us-west-1:
      AMI: "ami-0bdb828fd58c52235"
    us-west-2:
      AMI: "ami-a0cfeed8"
    eu-west-1:
      AMI: "ami-047bb4163c506cd98"
    sa-east-1:
      AMI: "ami-07b14488da8ea02a0"
    ap-southeast-1:
      AMI: "ami-08569b978cc4dfa10"
    ap-southeast-2:
      AMI: "ami-09b42976632b27e9b"
    ap-northeast-1:
      AMI: "ami-06cd52961ce9f0d85"

Parameters:
  EnvType:
    Description: Environment type.
    Default: test
    Type: String
    AllowedValues: [prod, dev, test]
    ConstraintDescription: must specify prod, dev, or test.

Conditions:
  CreateProdResources: !Equals [!Ref EnvType, prod]
  CreateDevResources: !Equals [!Ref EnvType, "dev"]

Resources:
  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      InstanceType: !If [CreateProdResources, c1.xlarge, !If [CreateDevResources, m1.large, m1.small]]
  MountPoint:
    Type: "AWS::EC2::VolumeAttachment"
    Condition: CreateProdResources
    Properties:
      InstanceId: !Ref EC2Instance
      VolumeId: !Ref NewVolume
      Device: /dev/sdh
  NewVolume:
    Type: "AWS::EC2::Volume"
    Condition: CreateProdResources
    Properties:
      Size: 100
      AvailabilityZone: !GetAtt EC2Instance.AvailabilityZone
```


### Best Practices

- Reuse Templates to Replicate Stacks in Multiple Environments.
- Nested Stacks can also used as a means to reuse common template patterns.
- Do not imbed credentials in your templates.
- Use input parameters to pass in information
  -  For additional security, you can use the NoEcho property to obfuscate the parameter value.
- Use Parameter Constraints - Don't allow your users to enter invalid parameter values
- Validate templates before using them
  - can use CloudFormation Designer to validate
  - can help you catch syntax and some semantic errors, such as circular dependencies
- VCS - This is code, so use best practices
  - code reviews
  - revision controls


### Drift

stack actions dd > detect drift > view drift results > view drift details











