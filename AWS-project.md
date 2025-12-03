- $VPC_ID = "vpc-0e920d7fe9b676f31"
- csd215-subnet-private = "SubnetId": "subnet-051e41c5c17877374"
- csd215-subnet-public = "SubnetId": "subnet-0d6efb89541fd8a15"
- csd215-rt-private = "RouteTableId": "rtb-058777c1f65825bf2"
- "AssociationId": "rtbassoc-0a0de5cb6f87600a2" // for private with table 
- csd215-rt-public = "RouteTableId": "rtb-01cb3c4a96883f5cd"
- "AssociationId": "rtbassoc-067051badad7f523c"  // for public
- "InternetGatewayId": "igw-053cda6f356cdc81f"
- $DDB_ENDPOINT_ID : "VpcEndpointId": "vpce-01a65c331f5972989",  // for dynano


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

5. Create an Internet Gateway
```
aws ec2 create-internet-gateway \
    --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=csd215-igw}]'
```

Attach the IGW to the VPC:

```
aws ec2 attach-internet-gateway \
>     --vpc-id vpc-0e920d7fe9b676f31 \
>     --internet-gateway-id igw-053cda6f356cdc81f
```

6. Default route for the public subnet to the IGW

```
aws ec2 create-route \
    --route-table-id rtb-01cb3c4a96883f5cd \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id igw-053cda6f356cdc81f
```

7. Create a VPC Endpoint Gateway to DynamoDB

https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-ddb.html
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/privatelink-interface-endpoints.html

```
aws ec2 create-vpc-endpoint \
    --vpc-id vpc-0e920d7fe9b676f31 \
    --vpc-endpoint-type Gateway \
    --service-name com.amazonaws.us-east-1.dynamodb \
    --route-table-ids rtb-058777c1f65825bf2 rtb-01cb3c4a96883f5cd \
    --tag-specifications 'ResourceType=vpc-endpoint,Tags=[{Key=Name,Value=csd215-dynamodb-endpoint}]'
```

Verify the Endpoint

```
aws ec2 describe-vpc-endpoints --vpc-endpoint-ids $DDB_ENDPOINT_ID
```

8. Set up a DynamoDB table
   
run `aws dynamodb create-table help` to open the full documentation.
<img width="1133" height="855" alt="image" src="https://github.com/user-attachments/assets/05e995e3-c9c2-4641-ad9a-aa4c4d20c62c" />

https://docs.aws.amazon.com/cli/latest/reference/dynamodb/create-table.html

Table name: dice-rolls  `--table-name <value>`
Billing mode: On-Demand (PAY_PER_REQUEST) `--billing-mode <value>`
Attributes: ` --attribute-definitions <value>`
source (String)
timestamp (Number)
Primary key:
<img width="455" height="141" alt="image" src="https://github.com/user-attachments/assets/6dd199f2-2af2-4738-8be1-b4c28b2b78a4" />

Partition key: source

Sort key: timestamp


