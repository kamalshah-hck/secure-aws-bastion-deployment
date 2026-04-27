# Secure Web Deployment with Bastion Host

I deployed a secure website architecture where the web server is kept private inside a private subnet, and direct public access is completely restricted. The security groups are configured in such a way that only the Bastion Host can connect to the private web server through a secure SSH connection.



The main question is: why is a Bastion Host required?

The Bastion Host provides a secure, controlled, and auditable entry point to access private servers from the public internet. Instead of exposing internal resources directly, all access is routed through this single hardened instance.



With this setup, public access to the private infrastructure is restricted, and only authorized users can access the private web server. This ensures better security by enforcing controlled access, accountability, and principles like integrity and non-repudiation from a cybersecurity perspective.



### Architecture
![Architecture](architecture.png)




### Steps

I started with creating the VPC. I created a VPC (vpc-001) with the CIDR block 10.0.0.0/16. After that, I created two subnets in two different availability zones. Although using two availability zones is not very critical for this project, as it mainly helps in high availability and disaster recovery, I still applied it to practice.



One subnet was a public subnet with the CIDR block 10.0.0.0/24 (around 250 usable IP addresses), and the other was a private subnet in a different availability zone with the CIDR block 10.0.1.0/24. I also created an Internet Gateway and attached it to the VPC to allow internet traffic.


After setting up the VPC and subnets, I created the security groups for both the Bastion Host and the Private Web Server EC2 instances so that they could be attached during instance creation.



* The Bastion Host security group allows inbound SSH traffic (port 22) only from my IP.
* The Web Server security group allows:

&#x20;       SSH (port 22) only from the Bastion Host

&#x20;       HTTP (port 80) for web access (used for testing)



After this, I launched the EC2 instances. The Bastion Host was deployed in the public subnet and has both public and private IP addresses, while the private web server EC2 instance was deployed in the private subnet with only a private IP address.

For testing, I used Git Bash to connect via SSH to the Bastion Host. From there, I connected to the private web server using its private IP. On the private instance, I deployed a basic HTML website.



To install the required packages on the private EC2 instance, I temporarily used a **NAT Gateway** to allow outbound internet access. After completing the setup, the **NAT Gateway** was no longer needed.



Finally, I verified the architecture by sending a request from the Bastion Host to the private web server using the curl command, which successfully returned the HTML response. This confirmed that the architecture is working as intended.


**Note: NAT Gateway** was used in this project only temporarily to install the required packages on the private EC2 instance. It is not included in the final architecture to reduce cost and maintain a minimal and secure design.


