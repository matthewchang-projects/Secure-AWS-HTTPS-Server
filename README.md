# Secure-AWS-HTTPS-Server

This project sets up a secure and scalable web server environment using AWS services. It leverages EC2 for hosting, an Application Load Balancer (ALB) for HTTPS termination, AWS Certificate Manager (ACM) for SSL/TLS certificates, and Route 53 for DNS management. 

<p align="center">
  <img src="https://github.com/user-attachments/assets/289b79a7-d2c8-4d63-9cac-97062267c1b8" alt="Centered Image" >
</p>

# Stage One

First we are going to create the infrastructure of this project starting with the VPC. Then we will move on assigning endpoints for the EC2 instance and then the instance itself.

- Head into the VPC console in your AWS account
- Select **Create VPC**
- Select **VPC and more**
- Leave everything as is but make sure to select **NAT GATEWAY in 1 az**
- Click Create
- Your Resource map should look like this when finished
![image](https://github.com/user-attachments/assets/263c605f-bcc4-4964-a7b5-77e77d056485)
- Once the VPC has been created move to [Route Tables](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#RouteTables:)
- Click on your Private subnet, **Edit Routes** and then **Add Route**
- For the Destination click on 0.0.0.0/0 and for the Target click on Nat Gateway
![image](https://github.com/user-attachments/assets/f0bfb56e-3d50-4982-8aad-1799a3457aa1)
- Now move to [Endpoints](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Endpoints:)
- Click on **Create Endpoint** and select EC2 Instance Connect Endpoint, select your VPC, you can leave the default security group, and for the subnet click on the Private 1 Subnet
- Now move to [Security Groups](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#SecurityGroups:)
- We are going to make Two Security Groups, for the first one Name it AllowSSH and the second AllowPublicInternetAccess
- Click **Create Security Group** and for the **Inbound Rule** SSH and source 0.0.0.0/0 and for the **Outbound Rule** all traffic, destination 0.0.0.0/0 and click Create
- Then click **Create Security Group** again and for the **Inbound Rule** allow https and source 0.0.0.0/0 and add another **Inbound Rule** allow http and source 0.0.0.0/0
- For the **Outbound Rule** all traffic, destination 0.0.0.0/0 and click create
- Once those have been created head to the [EC2 console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#Instances:)
- 

















