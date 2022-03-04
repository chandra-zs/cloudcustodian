# cloudcustodian

# Working with cloud custodian in single account
# Install cloud custodian
```
python3 -m venv custodian
source custodian/bin/activate
pip install c7n
```
# Create a policy (full access)

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
# Create a role and attach above policy

# Configure aws credentails in terminal 

```
aws configure
```
# Create a cloud Trail in aws account on which u want to execute policy and enable cloudwatch logs in that cloud trail

# Execute cloud custodian policies in single account

```
custodian run -s . custodian.yml
```
# Execute cloud custodian policies in single account and in all regions

```
custodian run -s . custodian.yml --regions all
```
# Cloud custodian for multiple accounts

# Go to .aws/config and place role arn created for u by child account

```
[profile pavan]
role_arn = arn:aws:iam::712621462878:role/Role-for-chandra
source_profile = default
```
# Check resources in pavan account
```
aws s3 ls --profile pavan
```
cd accounts-custodian/

# Install c7n-org for multi accounts

```
python3 -m venv custodian
source custodian/bin/activate
pip install pip --upgrade
pip install setuptools --upgrade
pip install c7n
pip install c7n-mailer
pip install c7n-org
```
# switch to directory

```
git clone https://github.com/zelarhq/zlr-int-poc-cloudcustodian.git
cd zlr-int-poc-cloudcustodian/
Cd policies/c7n-org
```
# To create accounts.yml file execute below command
```
python orgaccounts.py -f accounts.yml
```
# In below edit your role(Test-Role) click on role name Test_role and click on trust relationships and place below content

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::221773540150:root",
                    "arn:aws:iam::712621462878:role/Role-for-chandra"
                ],
                "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
       ]
     }
```     
# vi accounts.yml

```
accounts:
- account_id: '221773540150'
 display_name: chandrasekharyalamati
 email: chandrasekhar@zelarsoft.com
 name: chandrasekharyalamati
 #org_id: o-vy6kmz75ac
 role: arn:aws:iam::221773540150:role/Test-Role
 tags:
 - path:/
- account_id: '712621462878'
 display_name: pavan
 email: david@zelarsoft.com
 name: custodian
 #org_id: o-vy6kmz75ac
 role: arn:aws:iam::712621462878:role/Role-for-chandra
 tags:
 - path:/
```
# Exceute cloud custodian for multiple accounts

```
c7n-org run -c accounts.yml -s output -u ec2-check-tag.yml --region all
```
# Remove policies

```
c7n-org run-script -r all -s output -c accounts.yml "python3 ../../ops/mugc.py -c ../policies.yml"
```
# Limited permissions policy

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags",
                "ec2:DeleteTags",
                "ec2:DescribeTags",
                "ec2:DescribeInstances",
                "ec2:TerminateInstances",
                "ec2:DescribeVolumes",
                "ec2:DescribeRegions",
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```

