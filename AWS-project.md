$VPC_ID = "vpc-0e920d7fe9b676f31"
csd215-subnet-private = "SubnetId": "subnet-051e41c5c17877374"
csd215-subnet-public = "SubnetId": "subnet-0d6efb89541fd8a15"
csd215-rt-private = "RouteTableId": "rtb-058777c1f65825bf2"
"AssociationId": "rtbassoc-0a0de5cb6f87600a2" // for private with table 
csd215-rt-public = "RouteTableId": "rtb-01cb3c4a96883f5cd"
"AssociationId": "rtbassoc-067051badad7f523c"  // for public 


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

3. Enable auto-assign public IPv4 address on launch

```
aws ec2 modify-subnet-attribute \
>     --subnet-id subnet-0d6efb89541fd8a15 \
>     --map-public-ip-on-launch
```
Explanation:

`aws ec2 modify-subnet-attribute` This is the base command for modifying subnet attributes.

`--subnet-id subnet-id` Replace subnet-id with the actual ID of the subnet you want to modify.

`--map-public-ip-on-launch` This flag enables the auto-assignment of public IPv4 addresses for instances launched in that subnet.

4. Create Route Tables

https://docs.aws.amazon.com/cli/latest/reference/ec2/create-route-table.html

Frist: 

```
aws ec2 create-route-table \
    --vpc-id vpc-0e920d7fe9b676f31 \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=csd215-rt-private}]'
```

After:

```
aws ec2 associate-route-table \
    --subnet-id subnet-051e41c5c17877374 \
    --route-table-id rtb-058777c1f65825bf2
```
