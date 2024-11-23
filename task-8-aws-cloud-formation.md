
## Here we are creating network with cloudFormation


ICreate tje below listed resources, with AWS - Cloudformation !!

### Create VPC using cloudFormation 
![priview](images/cloudfrmn%20vpc.png)
![preview](images/cloudfrmn%20vpc2.png)
###  create Pub and Pvt subnets
![preview](./images/clf_pub&pvt%20subnets.png)
![preview](images/pub%20&pvt%20subnets.png)
### create IGW
![preview](images/cloudfrmn%20igw.png)
![preview](images/igw2.png)

### Attach IGW to VPC
![preview](images/igw%20attachment.png)
![preview](images/igw2.png)
- Create Pub and PVT RT
![preview](images/pub%20and%20pvt%20route%20-tables.png)
### Attach Pub sub to Pub rt
![preview](./images/association%20to%20pvt%20to%20puvt%20&pub%20to%20pub.png)

### Attach Pvt Sub to Pvt rt
![preview](./images/association%20to%20pvt%20to%20puvt%20&pub%20to%20pub.png)

### Attach IGW to Pub RT
![preview](./images/attach%20igw%20to%20pub%20RT.png)
![preview](./images/attach%20igw%20to%20pub%20RT2.png)

### Create Sg for ssh // http
![preview](./images/SGs1.png)
![preview](./images/SGs.png)
### Create a Ec2 in Pub Sub
![preview](./images/instance%20in%20pubsub.png)
### Create a Ec2 in Pvt Sub
![preview](./images/instance%20in%20pvtsub.png) 
![preview](./images/pub&pvt%20instances.png)
