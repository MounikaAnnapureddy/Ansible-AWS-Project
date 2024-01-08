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

**Now that we have created security groups for both the ansible maching and web servers, now we have to launch the EC2 instance for the ansible machine.**
# Launch the EC2 instance for the ansible machine
1. In the search bar look for EC2. Select EC2.
2. Click on Launch Instances.
3. Name it as ansible-machine.
4. Now scroll down to Application and OS Images (Amazon Machine Image) => "Quick Start  => select Amazon Linux", "Amazon Machine Image (AMI) => select Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type (free tier eligible)". 
5. Scroll down to Instance type => "Instance type => select t2.micro (free tier eligible)".
6. Scroll down to Key Pair (login) => "Create new key pair => name it whatever you want to but I named it as myec2key, key pairtype as RSA,  private key file format as .pem => click on create key pair".
7. Scroll down to Network Settings  => "click on  Edit => Keep the VPC value for the default VPC, For the subnet value it doesn't matter if we give a value or not however I have given the us-east-1a, Firewall(security groups) => select existing security group => click the dropdown of common security groups => select ansible machine's security group which is 'ansible machine sg' which we created earlier, atlast click on the Launch Instance".
8. We have successfully launched the EC2 instance for ansible machine. To see it, click on the instances or view all instances.

****Now that we have launched an EC2 instance for ansible machine, we need to install ansible on this EC2 instance, ssh into this instance and create a keypair. The keypair is used to establish an ssh connection between the ansible machine and the servers we want to configure**
# SSH into ansible server
1. In the AWS instances page where you can see the ansible machine that you have created,  in this case it is named as 'ansible-machine'. Select the ansible-machine.
2. Copy the Public IPv4 address.
3. If you are using putty give the host name as ec2-user@YOURPUBLICIPADDRESS. Replace YOURPUBLICIPADDRESS with the copied Public IPv4 address. Select SSH->Auth->Browse for the myec2key.pem file for the 'private key file for authentication' field -> click ope -> click yes.
   Alternatively, if you are using your linux terminal. you can use the below commands.
   bash
   cd "PATH TO THE DIRECTORY WHERE YOUR PEM FILE IS LOCATED"
   ls
   chmod 600 myec2key.pem
   ssh -i myec2key.pem ec2-user@YOURPUBLICIPADDRESS
4.  Now to create a key pair on the ansible machine. Run the command: ssh-keygen -t rsa -b 2048.
    And press enter thrice.
   Here the t stands for type and the type is rsa. b stands for bytes and the byte we want to create is 2048.
5. Now we have created a succesful key pair(public and private keys) and it is located in "/home/ec2-user/.ssh/"
6. Now let's go into this directory /home/ec2-user/.ssh/. Before, going into this directory, let's check our current working directory as we will be in home directory by default at this time. To check your present working directory, use the command: pwd. Press enter. You will see that you are already in /home/ec2-user directory.
7. All we have to do now is to change the directory to .ssh as we are already in /home/ec2-user directory. Use the command: cd .ssh. Once you enter this command, you will be in the .ssh directory.
8. Once you are in .ssh directory, give the command: ls which will lists the files of this directory. You must see the files authorized_keys, id_rsa, id_rsa.pub files.
   id_rsa is the private key, id_rsa.pub is the public key.

**Now that we have the public key, private key and authorized keys, we need to  import this public key into the EC2 instance, so that when we launch the servers we can this key to them**
# Import this public key into the EC2 instance (ansible-machine)
1. Print the content of the public key using the below command.
   cat id_rsa.pub
2. Now copy the content of the public key
3. Go to AWS console again and on the left side of the instance page, if you scroll down, you will network & security.
4. Under Network & Secuirty, select Key Pairs. On the top right, select Actions->import key pair -> name it whatever you wnat but I named it as ansible-public-key, under the Key Pair File field paste the public key we have copied from the terminal (the key you have copied after entering the command in step 1) -> click import key pair.

**Now that we have successfully imported the public key of a key pair we created on the ansible machine to the EC2 instance.So, when we launch the servers we want to connect to, we will add this keypair to those servers**
# Launch the servers to configure using ansible
1. Go to the AWS Console and click on EC2 dashboard.
2. Click on Launch instance
3. Name the instance. I named it as server.
4. To the right, you will see a field 'Number of Instances". Select 3 to create  3 instances at a time.
5. Now scroll down to Application and OS Images (Amazon Machine Image) => "Quick Start  => select Amazon Linux", "Amazon Machine Image (AMI) => select Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type (free tier eligible)".
6. Scroll down to Instance type => "Instance type => select t2.micro (free tier eligible)".
7. Scroll down to Key Pair (login) => "click on the key pair name drop down => select the key pair that we have created in the step 4 of Import this public key into the EC2 instance (ansible-machine). In this case, I named it as 'ansible-public-key' when I created it.".
8. Scroll down to Network Settings  => "click on  Edit => Keep the VPC value for the default VPC, For the subnet value it doesn't matter if we give a value or not however I have given the us-east-1b, Firewall(security groups) => select existing security group => click the dropdown of common security groups => select server's security group which is 'server sg' which we created earlier, atlast click on the Launch Instance".
9. We have successfully launched 3 EC2 instance for our web servers. To see it, click on the instances or view all instances.

**Now that we have successfully created 3 EC2 instances for the wweb servers, let's test the connection between the ansible machine and the 3 servers**
# Test the connection between the ansible machine and the 3 servers
1. Go to the terminal and change the directory from .ssh to home.
2. To change the directory to home, use the command: cd ~
3. To check if you are in the home directory or not, check your present working directory using the command: **pwd**.  You must see **/home/ec2-user** as your current directory. Now we are in the ansible machine
4. To test the connection, go to AWS instances -> select server -> copy the Private IPv4 address.
5. Come back to the terminal and enter the command: **ssh YOURPRIVATEIPADDRESS** and press enter and type yes and again press enter. Paste the Private IPv4 Address which we have copied from the instance in the place of YOURPRIVATEIPADDRESS.
6. The connection test should be successful. To verify if your connection is succesful or not, you can check which IP address is showing up after the completion of step 5. If it is success, you should see, [ec2-user@ip-**SERVERPRIVTAEIPADDRESS** ~]$ , which you have entered in the command: **ssh YOURPRIVATEIPADDRESS**.
7. To exit from the server's IP and come back to ansible machine, enter the command: **exit**. To verify if you are on the ansible maachine's connection or not, go to aws instances and select ansible-machine instance and verify if the private IPv4 address in the aws instances and the private IP address of the **[ec2-user@ANSIBLEMACHINEPRIVATEIP ~]** are the same. If they are same, that means you are now in ansible machine connection.
**REPEAT STEPS 1 TO 7 FOR THE REMAINING 2 SERVERS AS WELL TO TEST THE CONNECTION**

************TO ESTABLISH THE CONNECTION BETWEEN ANSIBLE MACHINE AND THE SERVERS WITH OUR KEY PAIR, THE PRIVATE KEY IS REMAINED ON THE ANSIBLE MACHINE, AND THE PUBLIC KEY IS SHARED WITH THE SERVERS. NOTE THAT, WE HAVE NOT INSTALLED ANSIBLE ON THE ANSIBLE MACHINE YET. THE CONNECTION IS ESTABLISHED USING THE KEY PAIRS AND SECURITY GROUPS************

**Now that we have successfully tested the connection between the ansible machine and the servers, we have to install the ansible on the ansible machine.**
# Install Ansible on Ansible machine
1. Update the EC2 instance using the command: **sudo yum update -y**
   
