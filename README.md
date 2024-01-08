# Ansible-AWS-Project
In this project we use ansible to deploy a website on multiple instances.

# Architecture to complete this project
1. First we will launch an EC2 instance called ansible machine and we will install ansible on it.
2. After that we will create a keypair on the ansible machine and use this keypair to establish a SSH connection between the ansible machine and the servers we want to configure.
3. To establish a SSH connection we will also create a security group that allows SSH access between the ansible machine and the servers.
4. Next we will launch 3 EC2 instances which are the servers we will configure with ansible and we will install the website on.
5. We will create an ansible playbook which will contains a list of commands also called as tags that we want to run on the servers.
6. Next we will also create an inventory file which contains the private IP address of the instances we want to configure and execute the playbook.
7. At last, Create the ansible configuration file which contains all the configuration assets we use during the runtime.

# Create ansible machine security group
1. Go to AWS console and in the search bar look for VPC. Select VPC.
2. On the VPC dashboard, to the left side of the page, scrolldown  to the Security and select security groups.
3. Click on Create Security Group.
4. Give the security group a name. Here, I named it as "ansible machine sg". I have given the same for the description as well. For the VPC field, click on the dropdown and select the default VPC.
5. In the inbound rules, click on add rule. The values for this rule would be: "Type: SSH, Source: My IP".
6. Scroll down and click on "Create Security Group".
   
# Create another security group that we will add to the web servers
1. Go to AWS console and in the search bar look for VPC. Select VPC.
2. On the VPC dashboard, to the left side of the page, scrolldown  to the Security and select security groups.
   Note: you don't have to repeat steps 1 & 2 if you are already on the security groups page
3. Click on Create Security Group.
4. Give the security group a name. Here, I named it as "server sg". I have given the same for the description as well. For the VPC field, click on the dropdown and select the default VPC.
5. In the inbound rules, click on add rule. The values for this rule would be: "Type: HTTP, Source: Anywhere IPv4".
6. In the inbound rules, click on add rule to add another rule. The values for this rule would be: "Type: SSH, Source: Custom; from the dropdown select 'ansible machine sg'".
7. Scroll down and click on "Create Security Group".

Now that we have created security groups for both the ansible maching and web servers, now we have to launch the EC2 instance for the ansible machine.
# Launch the EC2 instance for the ansible machine
1. In the search bar look for EC2. Select EC2.
2. Click on Launch Instances.
3. Name it as ansible-machine.
4. Now scroll down to Application and OS Images (Amazon Machine Image) => "Quick Start  => select Amazon Linux", "Amazon Machine Image (AMI) => select Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type (free tier eligible)". 
5. Scroll down to Instance type => "Instance type => select t2.micro (free tier eligible)".
6. Scroll down to Key Pair (login) => "Create new key pair => name it whatever you want to but I named it as myec2key, key pairtype as RSA,  private key file format as .pem => click on create key pair".
7. Scroll down to Network Settings  => "click on  Edit => Keep the VPC value for the default VPC, For the subnet value it doesn't matter if we give a value or not however I have given the us-east-1a, Firewall(security groups) => select existing security group => click the dropdown of common security groups => select ansible machine's security group which is 'ansible machine sg' which we created earlier, atlast click on the Launch Instance".
8. We have successfully launched the EC2 instance for ansible machine. To see it, click on the instances or view all instances.
