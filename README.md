# Intrinsic Functions



Used to send encoded data, usually user data

```yaml
Fn::Base64: AWS CloudFormation
```


### FindInMap

The intrinsic function Fn::FindInMap returns the value corresponding to keys in a two-level map that is declared in the Mappings section. 

```yaml
Fn::FindInMap: [ MapName, TopLevelKey, SecondLevelKey ]
```
