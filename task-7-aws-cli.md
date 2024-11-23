
The AWS CLI (Command Line Interface) is a tool that lets you manage your AWS services directly \
from your terminal or command prompt. It allows you to execute commands that interact with AWS services, \
automate tasks, and streamline the management of AWS resources.

To use AWS CLI, you need to:

Install the AWS CLI on your machine.
Configure it with your AWS credentials and region.
Here's an example of how to create a VPC using AWS CLI.

## Step 1: Install and Configure AWS CLI

Install AWS CLI (if not already installed):

For most systems, use pip install awscli or refer to the AWS CLI installation guide.

Configure AWS CLI with your credentials:

aws configure

Enter your Access Key ID, Secret Access Key, default region, and default output format (e.g., json).

## Step 2: Create a VPC Using AWS CLI
To create a Virtual Private Cloud (VPC), follow these steps:

# 1. CREATE VPC


Use the following command to create a new VPC with a specific CIDR block (e.g., 10.0.0.0/16):

bash
aws ec2 create-vpc --cidr-block 10.0.0.0/16

This command will output the VPC's information, including the VPC ID. Note the VPC ID as it’s required in the next steps.

2. Add a Name Tag to the VPC (Optional)
To add a name to the VPC for easier identification:

bash
aws ec2 create-tags --resources <VPC_ID> --tags Key=Name,Value=MyVPC

Replace <VPC_ID> with the actual VPC ID returned from the previous command.

# 3. CREATE SUBNETS

To create a subnet within the VPC, specify the VPC ID and the desired CIDR block for the subnet (e.g., 10.0.1.0/24):

bash
aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.0.1.0/24

4. Create an Internet Gateway and Attach it to the VPC
An Internet Gateway allows resources in the VPC to access the internet:

bash
# Create the Internet Gateway
aws ec2 create-internet-gateway

This command outputs the Internet Gateway ID. Use this ID to attach it to the VPC:

bash
aws ec2 attach-internet-gateway --vpc-id <VPC_ID> --internet-gateway-id <IGW_ID>

5. Create a Route Table and Associate it with the Subnet
A route table defines how traffic is directed within the VPC:

bash
# Create the route table
aws ec2 create-route-table --vpc-id <VPC_ID>

Take note of the Route Table ID and use it to create a route that directs internet-bound traffic to the Internet Gateway:

bash
aws ec2 create-route --route-table-id <RouteTable_ID> --destination-cidr-block 0.0.0.0/0 --gateway-id <IGW_ID>

Associate the route table with the subnet:

bash
aws ec2 associate-route-table --subnet-id <Subnet_ID> --route-table-id <RouteTable_ID>

This sets up a basic VPC with internet access. Each of these commands can be modified to suit specific requirements,\
like multiple subnets or custom routing rules.
