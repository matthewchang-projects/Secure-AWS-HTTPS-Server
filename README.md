# Secure-AWS-HTTPS-Server

This project sets up a secure and scalable web server environment using AWS services. It leverages **EC2** for hosting, an **Application Load Balancer (ALB)** for **HTTPS termination, AWS Certificate Manager (ACM) for SSL/TLS certificates, and Route 53 for DNS management**. 

<p align="center">
  <img src="https://github.com/user-attachments/assets/289b79a7-d2c8-4d63-9cac-97062267c1b8" alt="Centered Image" >
</p>

## Stage One

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
- Click on **Launch Instance** and select Ubuntu for the AMI, you can leave everthing else as is and scroll down to **Key pair** and select proceed without key pair
- For the **Network Settings** select the VPC we created and the Private 1a subnet, for the security group and the two that we have created
- Leave everything else as is and click **Launch instance**
- Once the instance has been created select **connect**
- Connect to the instance through **EC2 Instance Connect Endpoint**
![image](https://github.com/user-attachments/assets/51da233e-bbf3-451d-9dc1-8d10a4018a28)
- Once connected run **sudo apt update** and **sudo apt install nginx -y**
- Next run **sudo systemctl start nginx** and **sudo systemctl enable nginx**
- Then **cd /usr/share/nginx/html** and **sudo mv index.html index.backup.html**
- Finaly **sudo nano index.html** and then write your HTML

## Stage Two

Now that we have created most of our needed infrastructure we will now be creating our ALB and redirecting HTTPS traffic to the private subnet. After that we will be obtaining our SSL/TLS certificates and using Route 53 for DNS managment.

- Next move into the [Load Balancer Console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#LoadBalancers:)
- Click **Create Load Balancer** and **Application Load Balancer**
- Make sure its Internet-Facing and for VPC click the one we created
- For the subnets you can click public1 and public2
- For Security Groups click the allowspublicinternetaccess security group
- Once you scroll down to **Listeners and Routing** right click on **Create Target Group** and open it on a new tab
- In the new tab configure your target group by choosing **instances** and leaving everything else as is and click **next**
- Register your target as the EC2 we have created and click create target group
- Once it has been created move back into the Load Balancer tab and click on Default Option and you should see the your EC2 as the target group now
- You can now scroll down and create Load Balancer
![image](https://github.com/user-attachments/assets/67a00cb7-e9da-4858-98fc-30d9b69ea308)
- Now move to [Route 53] and click on **Create Hosted Zone**
- Once you have created the hosted zone move into [Certificate Manager](https://us-east-1.console.aws.amazon.com/acm/home?region=us-east-1#/certificates/request) and click request certificate
- Write your domain name and leave everything as is and click create
- Now click the certificate and click on **Create records in Route 53** and then **Create Records**
![image](https://github.com/user-attachments/assets/0928bdcf-3457-41a3-ade8-1ec950a91a3e)
- Move back into the [Load Balancer Console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#LoadBalancers:) and click on your Load Balancer and under **Listeners and Rules** click add listener
- For the **Protocol** click HTTPS and for the **Routing actions** click Foward to target groups
- For the target group choose your EC2 instance
- Under Secure listener settings choose the Certificate we just created with ACM
- Click Add and this is what it should look like
![image](https://github.com/user-attachments/assets/be97ee02-71e6-4d4b-ae6c-77575cadb659)
- Now click on the HTTP port 80 listener and edit listener
- Remove Foward to and change it to Redirect to URL and set the Port to 443
![image](https://github.com/user-attachments/assets/a563500e-1c19-434b-92ec-9ebe9d91f834)
- Click **Save Changes**
- Move back into [Route 53](https://us-east-1.console.aws.amazon.com/route53/v2/home?region=us-east-1#Dashboard)
- Go to the your Hosted zones and click your hosted zone
- **Create Record** and then **Quick Create Record**
- Click **Alias** and then Route traffic to Alias to Application and Classic Load Balancer
- Click the corresponding region and then the ALB we created and then Create Record
- Now search your domain the verify that your browser is routing correctly with HTTPS

<br>

![image](https://github.com/user-attachments/assets/b3b1b739-758c-40e6-a730-f381a85d9a35)




















