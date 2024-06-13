<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources in Azure
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in Active Directory
- Join Client-1 to my domain (mydomain.com)
- Create a bunch of additional users and attempt to log into client-1 with one of the users


<h2>Deployment and Configuration Steps</h2>

</p>
<p>
  
1. Setup Resources in Azure

<p>
  
</p>
First, I created the Domain Controller VM (Windows Server 2022) named "DC-1" in Microsoft Azure. 

Second, I set the Domain Controller's NIC Private IP Address to be static

![image](https://github.com/alexhannon/configure-ad/assets/168659572/fc0e40db-9383-4af8-94fa-c728c159d648)

Third, I Created the Client VM (Windows 10) named "Client-1". I then used the same Resource Group and Vnet that was created with the Domain Controller VM.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/19bd1936-5a98-47a8-85e1-0704e5a494a8)



2. Ensure Connectivity between the client and Domain Controller
<p>
  
</p>

First I logged into Client-1 with Remote Desktop and pinged DC-1's private IP address with ping -t <ipaddress> and the request timed out.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/6335b76c-fb5f-4345-95ae-6345a8f0fa91)

After this, I logged into the Domain Controller and enabled ICMPv4 rules on the local Windows firewall. 

![image](https://github.com/alexhannon/configure-ad/assets/168659572/1b243bd0-61cd-4f20-991e-ed1cc641bfef)

Third I then went back to Client-1 to see the ping succeed.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/20d7af68-dc70-4bc2-b2be-f1734a46e112)


3. Install Active Directory
<p>
  
</p>

First I went to Add Roles and Features in the Server Manager and added Active Directory Domain Services

![image](https://github.com/alexhannon/configure-ad/assets/168659572/be03a7d0-a4c4-48de-b6b3-6c88fc5939aa)

After this, I promoted as a Domain Controller and set a new forest as mydomain.com

![image](https://github.com/alexhannon/configure-ad/assets/168659572/cd672dfc-d174-4323-9d32-f8c1ed86810f)

Once my virtual machine restarted, I logged back into DC-1 as user: mydomain.com\labuser

![image](https://github.com/alexhannon/configure-ad/assets/168659572/3c06e24d-89bc-45ce-bb44-5bcc5603c1c2)

4. Create an Admin and Normal User Account in Active Directory
<p>
  
</p>

In Active Directory Users and Computers (ADUC), I created an Organizational Unit (OU) called "_Employees" and an OU called "_Admins".

![image](https://github.com/alexhannon/configure-ad/assets/168659572/dd0fa2f8-3b3c-4ce5-8ffd-f23b4bb457e6)

After this, I created a new admin named "Jane Doe" with the username of "jane_admin".

![image](https://github.com/alexhannon/configure-ad/assets/168659572/74cd8ccb-c24d-4f20-b90b-7a840b502301)

Then I added jane_admin to the "Domain Admins" Security Group.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/d6c668f0-e5eb-4102-8fb7-1f70becc4f90)

I then Logged out and logged back in as "mydomain.com\jane_admin

![image](https://github.com/alexhannon/configure-ad/assets/168659572/dbd573ff-1e5e-48ca-bc1e-ce1fb0c0f1c0)

5. Join Client-1 to your domain (mydomain.com)
<p>
  
</p>

From the Azure Portal, I set Client-1's DNS settings to the DC's Private IP address. Once I did that I restarted Client-1 from the Azure Portal.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/af6b313d-ec00-4932-9ad9-96dac9b26758)

I then logged back into Client-1 and joined it to the domain.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/9725fd3d-0834-44fe-9823-4e19d1cdcc85)

After this, I logged back into the Domain Controller and verified that Client-1 showed up in the ADUC. I then allowed "domain users" access to the remote desktop. With this, I would now log into Client-1 as a normal, non-administrative user.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/7263b5f8-594b-4ff6-a159-0f8e378dd6d6)

6. Create a bunch of additional users and attempt to log into client-1 with one of the users
<p>
  
</p>

To do this, I logged back into DC-1 as jane_admin. Once I did this I opened PowerShell_ise as an administrator. Once in there I created a new File and pasted the contents of a script into it. Script: (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

I ran the script and observed the accounts being created.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/8c53f9ac-566e-4254-aa67-4eb97333e3bc)

These were the accounts in the _Employees tab in the ADUC

![image](https://github.com/alexhannon/configure-ad/assets/168659572/b877dbb0-ad57-431a-91a6-ed2730f3fa89)

Finally, I logged into Client-1 with one of the accounts.

![image](https://github.com/alexhannon/configure-ad/assets/168659572/6cf2ae6d-cd0d-4909-914c-09d290280385)

![image](https://github.com/alexhannon/configure-ad/assets/168659572/52df5d2a-3f7d-42b1-9afa-d92291067a09)
