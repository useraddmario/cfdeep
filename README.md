# Intrinsic Functions



###Base64

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


### FindInMap

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


###Cidr

Returns an array of CIDR address blocks. The number of CIDR blocks returned is dependent on the count parameter. 

```yaml
Fn::Cidr: 
  - ipBlock 
  - count
  - cidrBits
```



###GetAtt

Returns the value of an attribute from a resource in the template.  Limited to what is available per resource, each resource definition had details of what is available.

```yaml
Fn::GetAtt: [ logicalNameOfResource, attributeName ]
```

```yaml
!GetAtt myELB.DNSName
```


###GetAZs

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


###ImportValue

Returns the value of an output exported by another stack. You typically use this function to create cross-stack references. You can't use the short form of !ImportValue when it contains a !Sub. 

```yaml
Fn::ImportValue:
  !Sub "${NetworkStackName}-SecurityGroupID"
```


###Join

Appends a set of values into a single value, separated by the specified delimiter. If a delimiter is the empty string, the set of values are concatenated with no delimiter. 

```yaml
!Join [ ":", [ a, b, c ] ]
```
returns: "a:b:c"

```yaml
!Join
  - ''
  - - 'arn:'
    - !Ref AWS::Partition
    - ':s3:::elasticbeanstalk-*-'
    - !Ref 'AWS::AccountId'
```


###Select

Returns a single object from a list of objects by index. 

```yaml
Subnet0:
  Type: "AWS::EC2::Subnet"
  Properties:
    VpcId: !Ref VPC
    CidrBlock: !Select [ 0, !Ref DbSubnetIpBlocks ]
```


###Split

Split a string into a list of string values so that you can select an element from the resulting string list.  Use !Select to pick one.

```yaml
!Split [ "|" , "a|b|c" ]
```











