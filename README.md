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

<h2>Additional Information</h2>

- How Can We Increase the Security of a Windows Server & the Resources Hosted by the Server?
- What Can be Used for Conducting a Security Risk Assessment on a Windows Server for HIPPA Compliance?
  
<h2>Deployment and Configuration Steps</h2>
<h3>Setup Virtual Machines in Azure</h3>
<p>
<img src="https://i.imgur.com/BmsB2eH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To set up our Active Directory environment, we need to create two virtual machines within Microsoft Azure. Create one virtual machine with Windows Server 2022 and name it DC-1, since this virtual machine will be our domain controller for Active Directory. Create another virtual machine with Windows 10 as the image and name it Client-1, since this virtual machine will simulate a computer that will be on Active Directory's domain in an organization. Before logging in to each virtual machine via Remote Desktop, change the DC-1's NIC private IP address to static so that the client's connection to the domain doesn't accidentally get lost. Additionally, make sure both virtual machines are in the same Resource Group and virtual network. Log in to DC-1 and go to the local windows firewall to enable ICMPv4 echo requests and replies so that Client-1 can communicate/connect to DC-1. 
</p>
<br />

<h3>Install Active Directory & Create an Admin Account</h3>
<p>
<img src="https://i.imgur.com/WfdYY3Y.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To install Active Directory on DC-1, go to add roles on Server Manager and check off Active Directory Domain Services when prompted, and install (pictured above). Then, we have to actually make the DC-1 virtual machine a domain controller. To do this, click the yellow icon on server manager to display a screen that is shown below. From here, select a new forest and name it whatever you desire (in this example it will be mydomain.com). Once complete, restart the virtual machine and log back in. 
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
Before creating non-administrative users in Active Directory, we need to allow non-administrative users to log in to Client-1. To do this, log in to the virtual machine as jane_admin since it is the administrative account and open system properties. Select Remote Desktop and then 'Select users that can remotely access this PC". Add Domain Users to this and select "OK", since domain users are a domain for every user that is created under Active Directory. 
</p>
<br />

<p>
<img src="https://i.imgur.com/bkJZNpR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To create non-administrative users in Active Directory, go back to the DC-1 virtual machine as jane_admin. Open Powershell ISE as an administrator, create a new file, and paste this script into it https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1. Select run (green arrow), and 10,000 users will be randomly generated, and put into the _EMPLOYEES Organizational Unit (note all have the password as Password1). You can now log into Client-1 with any of the new non-administrative users. Congratulations, you have successfully deployed Active Directory in a virtual environment!
</p>

<p>
<h3>How Can We Increase the Security of a Windows Server & the Resources Hosted by the Server?
</h3>
</p>
<p>
- Enable auditing for those accessing resources such as PHI hosted by the server, allowing administrators to see information on login attempts, resources accessed, etc. 
- Configure logs through Event Viewer to send out alerts if certain actions happen, such as if there is someone attempting to login from another country. You can also configure a SIEM so that all logs are aggregated to one place and further customized when you are alerted based on specified criteria. 
- Implement a patch management tool either from a third party or via Windows Server Update Services to schedule & plan to make security updates when they are released. 
- Configure role-based access control for users/groups on Active Directory to create a “least-privilege” environment. 
- Create password policies to ensure strong passwords, password rotations, locking-out procedures, and at least two-factor authentication (2FA) for maximum security of user accounts. 
- Encrypt all data hosted on the server, whether this is data at rest or in transit. Bitlocker is a tool that can implement encryption on the physical storage connected to the Windows Server. Installing backups of critical information such as PHI is beneficial in case of a ransomware attack or any malfunctions that may occur on the original hardware.
- Configure data loss prevention (DLP) & file integrity monitoring (FIM) to prevent PHI or any other personal information from leaving the secure enterprise network and to be notified if anyone is attempting to modify the integrity of critical files.
- Ensure that anti-virus/anti-malware protection is enabled on Windows Server. Windows Defender will conduct scans on the server and files to check if they match any signatures from known malware, then notify the administrator for further remediation. 
- Confirm with the manufacturer that the server is still receiving security support. If the server is at the end of service life (EOSL), plans need to be created to retire the server and purchase a new one that will be receiving continuous support. 
- Create an incident response & disaster recovery program. These will be extremely useful in the case that a malicious act is carried out on the server or the network that is connected to the server as well as if something physically happens to where the server is housed such as a natural disaster. These programs make everyone aware of the steps that will be carried out to ensure clear communication & responsiveness. 
</p>

<h3>What Can be Used for Conducting a Security Risk Assessment on a Windows Server for HIPPA Compliance? 
</h3>
<p>
- The Security Risk Assessment (SRA) tool from healthit.gov can be used to gather a risk assessment of your enterprise environment in relation to being in compliance with HIPPA regulations.
- The questions that the SRA tool asks are based on HIPPA regulations and the NIST Cybersecurity Framework (CSF).
- References are provided for each question asked in relation to HIPPA or the NIST CSF. 
- You can also attach reports/documentation to the SRA tool file, such as vulnerability scans, penetration test results, & assets related to electronic protected health information (PHI). 
- This tool will also allow you to input remediation steps for vulnerabilities that the tool deems your environment at risk for based on your answers. This makes it easier for documentation purposes by keeping it all within the risk assessment. 
- At the end of the assessment, a risk score will be assessed as well as areas for review to guide you to reach HIPPA compliance in your environment. You may save the entire security risk assessment as a file to easily share it with others. 
- You can read more about it and download it here: https://www.healthit.gov/topic/privacy-security-and-hipaa/security-risk-assessment-tool
</p>
<br />
