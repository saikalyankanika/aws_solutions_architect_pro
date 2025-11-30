## What should be known

- we can define zone_of_trust where , this area is the trusted are i mean access to this area is fine rest is danger and thus finds teh access outside of the zone_of_trust
- it also validates the iam policies against the best practices and its syntax and grammer
- and also it will also generate iam policy based on the actions of the service analysing and giving the best policy with least priviliges 
    - lets say for example we have an lambda calling s3 and kinesis data stream , based on the last 90 days logs of the cloudtrail it analyses and gives us teh iam policy 