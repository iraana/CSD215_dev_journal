1. Create the VPC
https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html
https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html

```
aws ec2 create-vpc \
>     --cidr-block 10.0.0.0/24 \
>     --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=csd215-vpc}]'
```
2. Create Subnets in the VPC

```
   aws ec2 create-subnet \
>     --vpc-id vpc-0e920d7fe9b676f31 \
>     --cidr-block 10.0.0.0/25 \
>     --availability-zone us-east-1a \
>     --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=csd215-subnet-private}]'
```
