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

- Setup Virtual Machines in Azure
- Install Active Directory & Create an Admin Account
- Join "Client-1" to the Active Directory Domain
- Use Powershell to Create Non-Administrative Users 

<h2>Deployment and Configuration Steps</h2>
<h3>Setup Virtual Machines in Azure</h3>
<p>
<img src="https://i.imgur.com/BmsB2eH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To set up our Active Directory environment, we need to create two virtual machines within Microsoft Azure. Create one virtual machine with Windows Server 2022 and name it as DC-1, since this virtual machine will be our domain controller for Active Directory. Create another virtual machine with Windows 10 as the image and name is as Client-1, since this virtual machine will simulate a computer that will be on Active Directory's domain in a organization. Before logging in to each virtual machine via Remote Desktop, change the DC-1's NIC private IP address to static so that the client's connection to the domain doesn't accidently get lost. Additionally, make sure both virtual machines are in the same Resource Group and virtual network. Log in to DC-1 and go to the local windows firewall to enable ICMPv4 echo requests and replies so that Client-1 can communicate/connect to DC-1. 
</p>
<br />

<h3>Install Active Directory & Create an Admin Account</h3>
<p>
<img src="https://i.imgur.com/WfdYY3Y.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To install Active Directory on DC-1, go to add roles on Server Manager and check off Active Directory Domain Services when prompted, and install (pictured above). Then, we have to actually make the DC-1 virtual machine a domain controller. To do this, click the yellow icon on server manager to display a screen that is shown below. From here, select new forest and name it whatever you desire (in this example it will be mydomain.com). Once complete, restart the virtual machine and log back in. 
</p>
<img src="https://i.imgur.com/yRv0Sal.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<p>
<img src="https://i.imgur.com/0FgxyAk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To create an administrative account in Active Directory, go to Tools in Server Manager and select Active Directory Users and Computers. From here create a new "user" with whatever name/username you desire (in this example the employee will be Jane Doe with jane_admin as its username). Once created, go to Properties on Jane and add her to the "Domain Admins" Security Group to give the account administrative privileges. Additionally, create two Organizational Units called "_EMPLOYEES" and "_ADMINS" to group different users. Log out of DC-1 and log back in with jane_admin to sign in as an admin in Active Directory.
</p>
<br />

<h3>Join "Client-1" to the Active Directory Domain</h3>
<p>
<img src="https://i.imgur.com/aU3m3OB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To join Client-1 to the Active Directory Domain (mydomain.com), change Client-1's DNS settings via Azure to map to DC-1's private IP address so that Client-1 recognizes mydomain.com (pictured above). Once changed, restart Client-1 on Azure and log in as the original user you created. Go to system settings and select "Rename this PC (advanced)", Change, and enter mydomain.com. The picture below is what will appear after joining Client-1 to the domain and will restart the virtual machine. Go back to DC-1 to see Client-1 appear in Active Directory under "Computers", then create another Organizational Unit named "_CLIENTS" and move Client-1 to this OU. 
</p>
<img src="https://i.imgur.com/BtkgPah.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

<h3>Use Powershell to Create Non-Administrative Users</h3>
<p>
<img src="https://i.imgur.com/5QIqSfa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Before creating non-administrative users in Active Directory, we need to allow non-administrative users to log in to Client-1. To do this, log in to the virtual machine as jane_admin since it is the administrative account, and open system properties. Select Remote Desktop and then 'Select users that can remotely access this PC". Add Domain Users to this and select "OK", since domain users are a domain for every user that is created under Active Directory. 
</p>
<br />

<p>
<img src="https://i.imgur.com/bkJZNpR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To create non-administrative users in Active Directory, go back to the DC-1 virtual machine as jane_admin. Open Powershell ISE as an administrator, create a new file and paste this script into it https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1. Select run (green arrow), and 10,000 users will be randomly generated, and put into the _EMPLOYEES Organizational Unit (note all have the password as Password1). You can now log into Client-1 with any of the new non-administrative users. Congratulations, you have successfully deployed Active Directory in a virtual environment!
</p>
<br />
