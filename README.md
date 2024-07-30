<h1>Windows Server Active Directory Management</h1>

<h2>Considerations</h2>

Conducting 

<h2>Description</h2>
The project involves two virtual machines: Windows Server 2022 with two NIC (  ) and Windows 11 (with the IP address 10.0.2.15). 
<br />


<h2>Utilities Used</h2>

- <b>Active Directory</b> 
- <b>PowerShell</b>

<h2>Environments Used </h2>

- <b>Windows Server 2022</b> 
- <b>Windows 11</b> 

<h2>Program walk-through:</h2>



<p align="center">
The Diagram: <br/>
<img src="https://imgur.com/HBmnFE4.png" height="80%" width="80%" alt=""/>



<br/>Run OpenVas on Kali Linux use the command:sudo gvm-start, go to the Administration tab, and check the Feed Status, make sure the feed has been updated, you can use the following command to update feed status in the terminal:

<br/>Update NVT: sudo greenbone-nvt-sync
<br/>Update SCAP: sudo greenbone-scapdata-sync
<br/>Update CERT: sudo greenbone-certdata-sync<br/>
<img src="https://imgur.com/2DNteDn.png" height="80%" width="80%" alt="Add Credential"/>

<br />
First we create new credential. To add a credential for an authenticated scan in GVM, go to the Configuration tab, select Credentials, click on the Add New Credential icon at the top left of the screen, name the credential 'MetasploitableVM'(you can choose other names), and enter the username and password as 'msfadmin'.  <br/>
<img src="https://imgur.com/p9w7qCN.png" height="80%" width="80%" alt="Add Credential"/>
<br />

<br />
Then we create new Host and target. To add a host in GVM, go to the Assets tab, select Hosts, click on the Add New Host icon at the top left of the screen, name the host Metasploitable, and type in its IP address.   <br/>
<img src="https://imgur.com/EI849fV.png" height="80%" width="80%" alt="Add New Host"/>
<br />
