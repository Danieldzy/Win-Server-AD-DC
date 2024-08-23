<h1>Windows Server Active Directory Management Home Lab</h1>

<h2>Considerations</h2>
In a real office environment, best practice suggests that a Domain Controller (DC) should have only one internal-facing network interface card (NIC) for security purposes. Furthermore, DCs and workstations should be placed on different subnets to enhance network isolation. Internet access should be routed through a proxy server and a firewall or router, ensuring the DC remains securely dedicated to its internal role.<br/>


<br/>A Domain Controller (DC) should not be public-facing for these key reasons:<br/>

1. Security: DCs store sensitive data like user credentials, making them prime targets for attacks if exposed to the internet.<br/>
2. Expanded Attack Surface: Public exposure increases the risk of exploitation through vulnerabilities in services like LDAP or DNS.<br/>
3. Core Role: DCs handle critical internal tasks like authentication, and managing group policies. Which should remain within the internal network.<br/>
4. Performance Impact: External traffic can degrade the DC's ability to efficiently perform vital internal operations.<br/>

<br/>It is also advisable to keep services like Remote Access Services (RAS) and other non-core functions off the DC. RAS should be deployed on a dedicated server, allowing the DC to focus on its core responsibilities without being subjected to additional security risks or performance issues.

<br/>In a home lab environment, however, we have assigned two NICs to the DC (one internal and one external) to demonstrate the lab. This setup should never be used for real-world scenarios but serves to illustrate how to connect a workstation to the DC using Active Directory and allow the workstation to access the internet. Thus, the lab setup differs from the real-world configuration for educational purposes. Diagram 1 below should be the real-world scenario. Diagram 2 later is the demonstration of our home lab.<br/>
 
<p align="center">
Diagram 1: <br/>
<br/>
<img src="https://imgur.com/b94665x.png" height="80%" width="80%" alt=""/>


<h2>Overview</h2>
The project involves two virtual machines: Windows Server 2022 (DC) with two NICs (NIC one: 10.0.2.15 DHCP to Internet. NIC two: 172.16.0.1 Static assigned) and Windows 11 (Internal network access to the Internet through DC RAS NAT configured).  Later we create new users (_USERS OU) in Active Directory. Connect Windows 11 Workstation to Windows Server 2022 Domain Controller(DC) and access to the Internet.<br/>
<br />


<h2>Utilities Used</h2>

- <b>Active Directory</b> 
- <b>PowerShell</b>

<h2>Environments Used </h2>

- <b>Windows Server 2022 (DC)</b> 
- <b>Windows 11 (Workstation)</b> 

<h2>Program walk-through:</h2>


<p align="center">
Diagram 2: <br/>
<br/>
<br/><img src="https://imgur.com/pDM6OOQ.png" height="80%" width="80%" alt=""/>

<br/>First, in VirtualBox VM settings, select network, and ensure Win Server DC VM Internal NIC(adapter) and Win11 workstation VM adapter are in the same Internal network and have the same name(intnet in this case).<br/>
<img src="https://imgur.com/XHH8omh.png" height="80%" width="80%" alt="Add Credential"/>


<br/>The services we have on our Windows Server 2022 DC server manager: 
<br/>1, AD DC to create our FQDN (incdomain.com) and promote it to Domain Controller(DC), then we can use PowerShell to create users.<br/> 
<br/> 2, DHCP with router function enabled to auto assign IP address to Win11 workstation.<br/>
<br/>3, Remote Access Services (RAS) with NAT (Network Address Translation) enabled. Facilitates the connection of multiple devices on a local network to the internet using a single public IP address (This not recommended in real-world scenarios, but just for this home lab). With this feature installed, our Windows 11 Workstation can communicate with the Internet through our Windows Server 2022 DC.<br/>
<img src="https://imgur.com/B8q0o09.png" height="80%" width="80%" alt="Add Credential"/>


<br />Install Active Domain services in Server Manager (use Add roles and features), add new Organization Units (OU) named ADMINS, Inside this OU, add a new user as the administrator. You will also need to promote this machine as Domain Controller after installation:<br />
<img src="https://imgur.com/Bx4FBUT.png" height="80%" width="80%" alt="Add New Host"/>

<br />Install DHCP services in Server Manager (use Add roles and features). Your will also need to enable the Router function from DHCP service, go to Server options and select 003 Router then finish the Wizard and reboot Server Manager:<br />
<img src="https://imgur.com/CJgGmUh.png" height="80%" width="80%" alt="Add New Host"/>

<br />Install RAS(Remote access) services in Server Manager (use Add roles and features). Choose NAT(Network Address Translation), Make sure to select the DHCP NIC to finish the Wizard, so our workstation(Win11) can access the internet:<br />
<img src="https://imgur.com/Nq6Jo59.png" height="80%" width="80%" alt="Add New Host"/>

<br />
<br />After setting up AD DC, DHCP and RAS services. Let's use PowerShell script for create users, the PowerShell scripts and name.txt can be downloaded from this repository. Open your PowerShell as an administrator (Note it is better to open the PowerShell in the same directory as your name.txt is stored, so it can fetch the document, if not, you need to assign the $PATH variable and point it to the name.txt file):<br />
<img src="https://imgur.com/azpPcer.png" height="80%" width="80%" alt="Add New Host"/>
<br />$PASSWORD_FOR_USERS = "Password1"<br />
This line sets the variable $PASSWORD_FOR_USERS to the string "Password1". This means that the password "Password1" is now stored in this variable and can be used later in your script.<br />

<br />$USER_FIRST_LAST_LIST = Get-Content .\names.txt<br />
Get-Content .\names.txt: Reads the contents of the names.txt file.
$USER_FIRST_LAST_LIST: Stores the contents of the file in an array variable. <br/>

<br />$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force<br />
ConvertTo-SecureString: This cmdlet converts a plain text string into a secure string, which is encrypted in memory. This is useful for handling sensitive information like passwords securely.<br/>
-AsPlainText: This parameter specifies that the input string should be treated as plain text. Without this, PowerShell would expect the input to be already encrypted. -Force means intentionally converting plain text to a secure string, overriding any warnings. And finally, this input string has been stored as a new variable $password<br/>

<br />New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false<br />
New-ADOrganizationalUnit: This cmdlet is used to create a new Organizational Unit in Active Directory.-Name _USERS: Specifies the name of the new Organizational Unit. In this case, the OU will be named _USERS.
<br />-ProtectedFromAccidentalDeletion $false: This parameter means the OU is not protected, so it can be deleted by mistake or intentionally if needed. This is a lab environment, therefore we set it to $false for demonstration purposes. If you want the OU to be protected from accidental deletion, you would set this parameter to $true:

<br /> Now let us delve into the second part of the scripts, the for loop<br />
<img src="https://imgur.com/azpPcer.png" height="80%" width="80%" alt="Add New Host"/>

<br />foreach ($n in $USER_FIRST_LAST_LIST) {
    <br />$first = $n.Split(" ")[0].ToLower()
    <br />$last = $n.Split(" ")[1].ToLower()<br />
<br />This PowerShell code is starting to split each name in $USER_FIRST_LAST_LIST into first and last names, converting the first name to lowercase. It uses a space " " to separate first and last name.

<br />$username = "$($first.Substring(0,1)$last)".Tolower()
<br />This line of PowerShell code is used to create a username by combining the first initial of the first name with the full last name, and then converting the entire username to lowercase. If the name is John Doe, then it will become 'jdoe' and be stored in the username variable<br />
<br />Write-Host "Creating user: $username" -BackgroundColor Black -ForegroundColor Cyan<br />
Each time the user has been created when running the script, a message Creating user: xxx will be printed out.<br />


<br />Last part of the script<br />
<img src="https://imgur.com/LwoCrqn.png" height="80%" width="80%" alt="Add New Host"/>
<br />This part creates a new user and uses the variable we defined earlier to assign to the required field. <br />
<br />-PasswordNeverExpires $true: Specifies that the password for the user will never expire. In real work environment, you want to set it to false to implement more strict password policy<br />
<br />-Path "ou=_USERS,$(([ADSI]"").distinguishedName)": Sets the path in AD where the user will be created. This concatenates the organizational unit _USERS` with the distinguished name of the domain.<br />
<br />-Enabled $true: Enables the user account upon creation.<br />

<br />Next we run the script, you need to run this PowerShell inside the same directory where the name.txt is stored since it will pull all the information from name.txt. Or you will have to define another $file_path variable to target the name.txt file. <br />
<img src="https://imgur.com/JczTrHu.png" height="80%" width="80%" alt="Add New Host"/>
<br />When the scripts runs, you can see it has printed out the "Creating user:xxx" message, and going to Active Directory User and Computers GUI, you will see the new OU _USERS has been created and all the new users has been created.<br />
<img src="https://imgur.com/CLO6Ejd.png" height="80%" width="80%" alt="Add New Host"/>


<br />Now, we will connect our Windows 11 machine to our domain, establishing a simulated enterprise network management environment. Note that the Windows 11 Workstation VM cannot be Home Edition, as it does not support joining an Active Directory Domain.<br />
<br />After booting up the Win11 Workstation, go to Network Connections and set the IPv4 to DHCP, since this workstation is in the same internal network as our Windows Server DC (We set it up at the beginning), it will automatically get the first IP address from our Windows Server DC DHCP scope which is 172.16.0.100. From there, you can ping our DC and www.google.com, it will get the response.<br />
<img src="https://imgur.com/aPoK1ek.png" height="80%" width="80%" alt="Add New Host"/>
