## What should be known

- AWS STS stands for AWS Security Token Service
- provide access to a iam user in same account or cross account, or any 3 rd party user needs access to assume role (identity federation)
- this also has ability to have the session revoke till a specific time 
- when assuming a role it will lose its own identity and assumes and acquired teh identity of the role it assumed 
- provide access to aws accounts owned by third party aws account:
    ### What is the problem?
    - When we allow a **third-party AWS account** (like a monitoring tool or vendor) to assume a role in our account, there is a risk.
    - A malicious person could trick that third party into using its permissions to access our resources.
    - This is called the **Confused Deputy problem**.

    ### What is ExternalID?
    - **ExternalID** is a unique string we create and share only with the trusted third party.
    - When the third party assumes our role, they must provide this ExternalID.
    - If the ExternalID does not match, AWS denies the request.
    - This ensures that only the intended third party can use the role.

    ### Example Trust Policy with ExternalID
    ```
    {
    "Version": "2012-10-17",
    "Statement": [
        {
        "Effect": "Allow",
        "Principal": { "AWS": "arn:aws:iam::111122223333:root" },
        "Action": "sts:AssumeRole",
        "Condition": {
            "StringEquals": {
            "sts:ExternalId": "my-unique-external-id-12345"
            }
        }
        }
    ]
    }
    ```
 - we can also have tags in session whcih can be easily identified or having permiison will be simplified in iam policies for resoruces \
