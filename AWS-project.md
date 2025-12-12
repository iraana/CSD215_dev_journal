- $VPC_ID = "vpc-0e920d7fe9b676f31"
- csd215-subnet-private = "SubnetId": "subnet-051e41c5c17877374"
- csd215-subnet-public = "SubnetId": "subnet-0d6efb89541fd8a15"
- csd215-rt-private = "RouteTableId": "rtb-058777c1f65825bf2"
- "AssociationId": "rtbassoc-0a0de5cb6f87600a2" // for private with table 
- csd215-rt-public = "RouteTableId": "rtb-01cb3c4a96883f5cd"
- "AssociationId": "rtbassoc-067051badad7f523c"  // for public
- "InternetGatewayId": "igw-053cda6f356cdc81f"
- $DDB_ENDPOINT_ID : "VpcEndpointId": "vpce-01a65c331f5972989",  // for dynano
- "TableId": "8ff61b82-7fc9-4b32-a7e4-8ee04fdc7f01" // dice-rolls
- "GroupId": "sg-088e04f68667f70c1" // security-group --group-name csd215-ec2-sg
- "InstanceProfileId": "AIPASVODC3Z2EIWMGE5A7"
- Instance Id : "i-0871790be966d8e05"
- "GroupId": "sg-0ae0946176c7fa582", // security group for lamnda
- 


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
---
```
aws dynamodb create-table \
    --table-name dice-rolls \
    --billing-mode PAY_PER_REQUEST \
    --attribute-definitions \
        AttributeName=source,AttributeType=S \
        AttributeName=timestamp,AttributeType=N \
    --key-schema \
        AttributeName=source,KeyType=HASH \
        AttributeName=timestamp,KeyType=RANGE
```

9. Create a security group within your VPC

https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html

10. Launch EC2 Instance

https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html
```
aws ec2 run-instances \
    --image-id ami-0fa3fe0fa7920f68e \
    --count 1 \
    --instance-type t2.nano \
    --key-name csd215-keypair \
    --iam-instance-profile Name=csd215-instance-profile \
    --security-group-ids sg-088e04f68667f70c1 \
    --subnet-id subnet-0d6efb89541fd8a15 \
    --associate-public-ip-address \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=csd215-flask-instance}]' \
    --user-data file://resources/user_data.sh
```

11. Lambda function
```
aws lambda create-function \
  --function-name csd215-lambda \
  --runtime python3.9 \
  --role arn:aws:iam::183482113652:role/LabRole \
  --handler lambda_app.main \
  --vpc-config SubnetIds=subnet-051e41c5c17877374,SecurityGroupIds=sg-0ae0946176c7fa582 \
  --zip-file fileb://resources/lambda_placeholder.zip
```

<img width="1470" height="750" alt="image" src="https://github.com/user-attachments/assets/467937f1-d1c1-4c06-b835-eeaa77379006" />
<img width="1470" height="843" alt="image" src="https://github.com/user-attachments/assets/e1d9d843-130c-43bf-b08f-785bc82df35f" />


12. Lambda cost calculation 
```
Unit conversions
Amount of memory allocated: 128 MB x 0.0009765625 GB in a MB = 0.125 GB
Amount of ephemeral storage allocated: 512 MB x 0.0009765625 GB in a MB = 0.5 GB
Pricing calculations
1,000,000 requests x 200 ms x 0.001 ms to sec conversion factor = 200,000.00 total compute (seconds)
0.125 GB x 200,000.00 seconds = 25,000.00 total compute (GB-s)
Tiered price for: 25,000.00 GB-s
25,000 GB-s x 0.0000166667 USD = 0.42 USD
Total tier cost = 0.4167 USD (monthly compute charges)
Monthly compute charges: 0.42 USD
1,000,000 requests x 0.0000002 USD = 0.20 USD (monthly request charges)
Monthly request charges: 0.20 USD
0.50 GB - 0.5 GB (no additional charge) = 0.00 GB billable ephemeral storage per function
Monthly ephemeral storage charges: 0 USD
0.42 USD + 0.20 USD = 0.62 USD
Lambda cost (monthly): 0.62 USD
```
! without free tier

13. EC2 cost

```
1 instances x 0.0058 USD On Demand hourly cost x 730 hours in a month = 4.234000 USD
On-Demand instances (monthly): 4.234000 USD
```
14. EC2 vs Lambda function
```
EC2: 1 instances x 0.0058 USD On Demand hourly cost x 730 hours in a month = 4.234000 USD/month
Lamda:
Monthly request charge - 1,000,000 requests x 0.0000002 USD = 0.20 USD/month
Monthly compute charges - 25,000 GB-s x 0.0000166667 USD = 0.42 USD/month
Lambda cost (monthly): 0.62 USD
Price for one requesr for Lambda : 0.62 / 1000000 = 0.00000062 USD/request
So, (EC2 montly cost)/(Lambda cost per request) = 4.234000 / 0.00000062 = 6,829,032.2581 requests/month
Lamda function can handle roughly 6.8 million requests per month before it becomes more expensive than running the t2.nano EC2 instance
```

