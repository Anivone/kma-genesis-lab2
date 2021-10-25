# kma-genesis-lab2

Create VPC:  
`aws ec2 create-vpc --cidr-block 10.0.0.0/18 --no-amazon-provided-ipv6-cidr-block --query Vpc.VpcId --output text`  

Create 3 Subnets:\
`aws ec2 create-subnet --vpc-id <my-vpc-id> --cidr-block 10.0.1.0/24`\
`aws ec2 create-subnet --vpc-id <my-vpc-id> --cidr-block 10.0.2.0/24`\
`aws ec2 create-subnet --vpc-id <my-vpc-id> --cidr-block 10.0.3.0/24`
