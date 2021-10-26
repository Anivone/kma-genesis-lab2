# kma-genesis-lab2

## 1. Create VPC:
```
aws ec2 create-vpc --cidr-block 10.10.0.0/18 --no-amazon-provided-ipv6-cidr-block --query Vpc.VpcId --output text
```

## 2. Create 3 Subnets:
```aws ec2 create-subnet --vpc-id <my-vpc-id> --cidr-block 10.10.1.0/24 --availability-zone eu-central-1a```\
```aws ec2 create-subnet --vpc-id <my-vpc-id> --cidr-block 10.10.2.0/24 --availability-zone eu-central-1b```\
```aws ec2 create-subnet --vpc-id <my-vpc-id> --cidr-block 10.10.3.0/24 --availability-zone eu-central-1c```

## 3. Create Launch Template:
```
aws ec2 create-launch-template \ 
--launch-template-name kma-genesis \ 
--version-description TestVersion1 \ 
--launch-template-data '{"NetworkInterfaces": [{"DeviceIndex":0,"AssociatePublicIpAddress":true,"Groups":["<my-vpc-security-group-id>"]}], "ImageId": "ami-058e6df85cfc7760b", "InstanceType": "t2.xlarge", "KeyName": "key-pair.secret"}'
```

## 4. Create ASG:
```
aws autoscaling create-auto-scaling-group \
--auto-scaling-group-name my-asg \
--launch-template LaunchTemplateId=<my-launch-template-id> \
--min-size 1 \
--max-size 3 \
--vpc-zone-identifier "<my-vpc-subnet-id1>, <my-vpc-subnet-id2>, <my-vpc-subnet-id3>"
```

## 5. Allow connections to TCP ports 22 (SSH), 80 (HTTP), 443 (HTTPS)
```
aws ec2 authorize-security-group-ingress \
--group-id sg-02aca5d91d1709da4 \
--protocol tcp \
--port 22 \
--cidr 10.10.0.0/18
```
```
aws ec2 authorize-security-group-ingress \
--group-id sg-02aca5d91d1709da4 \
--protocol tcp \
--port 80 \
--cidr 10.10.0.0/18
```
```
aws ec2 authorize-security-group-ingress \
--group-id sg-02aca5d91d1709da4 \
--protocol tcp \
--port 443 \
--cidr 10.10.0.0/18
```
```
aws ec2 authorize-security-group-egress \
--group-id sg-02aca5d91d1709da4 \
--protocol tcp \
--port 22 \
--cidr 10.10.0.0/18
```
```
aws ec2 authorize-security-group-egress \
--group-id sg-02aca5d91d1709da4 \
--protocol tcp \
--port 80 \
--cidr 10.10.0.0/18
```
```
aws ec2 authorize-security-group-egress \
--group-id sg-02aca5d91d1709da4 \
--protocol tcp \
--port 443 \
--cidr 10.10.0.0/18
```

## 6. Create Internet Gateway and attach it to VPC

```
aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
```
```
aws ec2 attach-internet-gateway --internet-gateway-id <my-ig-id> --vpc-id <my-vpc-id>
```

## 7. Create Load Balancer

```
aws elbv2 create-load-balancer --name my-load-balancer  \
--subnets <my-vpc-subnet-id1> <my-vpc-subnet-id2> --security-groups <my-vpc-security-group-id>
```

## 8. Create Target Group for Load Balancer

```
aws elbv2 create-target-group --name my-targets --protocol HTTP --port 80 \
--vpc-id <my-vpc-id>
```

## 9. Register my Instance to the Target Group

```
aws elbv2 register-targets --target-group-arn <my-target-group-arn>  \
--targets Id=<my-vpc-instance-id>
```

## 10. Create a listener for my load balancer with a default rule that forwards requests to my target group

```
aws elbv2 create-listener --load-balancer-arn <my-load-balancer-arn> \
--protocol HTTP --port 80  \
--default-actions Type=forward,TargetGroupArn=<my-target-group-arn>
```
