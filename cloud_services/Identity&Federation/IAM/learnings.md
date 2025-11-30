## What should be known

- Users: Long term credentials
- Groups
- Roles: short-term credentials, uses STS service, examples are service roles to perform actions by services on different services inside AWS.
- Policies:
    - Define what a role or user can do 
    - Types:
        - AWS Managed
        - Customer managed
        - Inline policies
    - Resource-based policies

- IAM policy anatomy is:
    - Effect
    - Action
    - Resource
    - conditions
    - policy variables
- explicit DENY is having precedence over ALLOW
- least priviliges is teh best practice
- Access Advisor is the tol whcih can be validated for teh usage of policies
- Access Analyzer: analyse resources are shared with external entities
- example of administrative access: 
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```
- example of PowerUserAccess:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": [
        "iam:*",
        "organizations:*",
        "account:*"
      ],
      "Resource": "*"
    }
  ]
}
```
- there are conditions like stringEquals,stringNotEquals..etc.. and examples are :
```
{
  "Condition": {
    "StringEquals": {
      "aws:RequestedRegion": "us-east-1"
    },
    "StringLike": {
      "s3:prefix": "home/*"
    },
    "StringNotEquals": {
      "aws:RequestedRegion": "us-west-2"
    },
    "NumericLessThan": {
      "s3:max-keys": 10
    },
    "NumericGreaterThan": {
      "ec2:VolumeSize": 100
    },
    "Bool": {
      "aws:SecureTransport": "true"
    },
    "DateGreaterThan": {
      "aws:CurrentTime": "2025-01-01T00:00:00Z"
    },
    "DateLessThan": {
      "aws:CurrentTime": "2025-12-31T23:59:59Z"
    },
    "IpAddress": {
      "aws:SourceIp": "203.0.113.0/24"
    },
    "NotIpAddress": {
      "aws:SourceIp": "198.51.100.0/24"
    }
  }
}
```
- Resource based policy is applied on the resources like S3 , snss, sqs, lambda,efs,ecr, etc...
- there is permissions boundaries also can setup for users and roles , this acts as a upper limit to the role permissions lets have in permission boundary there is deny command for s3 get and there s3 get allow in the role permission then the user will not get any access, this acts as an intersection fo both policies riules, like an OR condition the intersection of both rules actually applies to the roles , an example is given below

- **IAM Permissions (Policies):** Define what actions a user/role *can perform* on AWS resources.
- **Permission Boundaries:** Define the *maximum allowed permissions* (upper limit). They act as a guardrail.
- **Effective Permissions:** The intersection of IAM policy and permission boundary.  
  - If either denies → the action is denied.  
  - Both must allow → the action is allowed.


### IAM Role Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}

permission boundary is :

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "s3:GetObject",
      "Resource": "*"
    }
  ]
}
```
### visualization will be like below:
---
```
+-------------------+       +-------------------------+
|   IAM Policy      |       |  Permission Boundary    |
|   (Grants access) |       |  (Limits access)        |
+-------------------+       +-------------------------+
            \                         /
             \                       /
              \                     /
               \                   /
                \                 /
                 \               /
                  \             /
                   \           /
                    \         /
                     \       /
                      \     /
                       \   /
                        \ /
                 +-------------------+
                 | Effective         |
                 | Permissions       |
                 | (Intersection)    |
                 +-------------------+
```