<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure): A Practical Guide to Setting up a Domain Controller and Users</h1>
This tutorial provides a comprehensive walkthrough on deploying on-premises Active Directory and creating user accounts within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create the Domain Controller and Client VMs
- Configure the NIC Private IP address of the Domain Controller (DC-1) to static
- Enable ICMPv4 in the local Windows Firewall of DC-1
- Install Active Directory on the Domain Controller
- Promote DC-1 to a domain controller
- Create Organizational Units and an Admin Account in Active Directory
- Connect Client-1 to the domain
- Create users using a Powershell script
- Login to Client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

<p>
We will start by creating the Domain Controller and Client VMs. Set up the Domain Controller VM (Windows Server 2022) named "DC-1". Note the Resource Group and Virtual Network (Vnet) created during the VM setup.
<p align="center"><img src="https://i.imgur.com/7qrgRpY.png" height="70%" width="70%" alt="create domain controller"/> </p>
<p align="center"><img src="https://i.imgur.com/BnkagLe.png" height="70%" width="70%" alt="create domain controller"/> </p>
</p>
<br />

<p>
Create the Client VM (Windows 10) named "Client-1" using the same Resource Group and Vnet as the Domain Controller.
<p align="center"><img src="https://i.imgur.com/TXSSMHC.png" height="70%" width="70%" alt="create client"/> </p>
<p align="center"><img src="https://i.imgur.com/QtCMdlv.png" height="70%" width="70%" alt="create client"/> </p>
<p align="center"><img src="https://i.imgur.com/tpfoksR.png" height="70%" width="70%" alt="create client"/> </p>
</p>
<br />

<p>
Let's configure the Domain Controller's NIC Private IP address to static. This is required to allow the Client VM to join the domain. Locate DC-1 select Networking -> Network Interface -> IP configurations -> ipconfig1 and switch the Assignment to "Static".
<p align="center"><img src="https://i.imgur.com/lWpDmsz.png" height="70%" width="70%" alt="configure ip"/> </p>
<p align="center"><img src="https://i.imgur.com/KBiqAks.png" height="70%" width="70%" alt="configure ip"/> </p>
<p align="center"><img src="https://i.imgur.com/KntdwZd.png" height="50%" width="50%" alt="configure ip"/> </p>
</p>
<br />

<p>
Now login to the Domain Controller so that we can enable ICMPv4 in the local Windows Firewall. This will allow ping requests between the Client and Domain Controller.
<p align="center"><img src="https://i.imgur.com/ljWRm1o.png" height="70%" width="70%" alt="enable icmpv4"/> </p>
</p>
<br />

<p>
From DC-1's desktop click start and search "Windows Defender Firewall with Advanced Security". Select Inbound Rules and sort by Protocol for easier navigation. Now right click the two Inbound Rules named "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)" and select Enable Rule.
<p align="center"><img src="https://i.imgur.com/kCc5RqN.png" height="70%" width="70%" alt="enable icmpv4"/> </p>
<p align="center"><img src="https://i.imgur.com/FlTZqpz.png" height="70%" width="70%" alt="enable icmpv4"/> </p>
<p align="center"><img src="https://i.imgur.com/md2X3H9.png" height="70%" width="70%" alt="enable icmpv4"/> </p>
</p>
<br />

<p>
Go back to Client-1 and initiate a perpetual ping (ping -t) to confirm the connection betweeen the Client and Domain Controller.
<p align="center"><img src="https://i.imgur.com/cbMSf2I.png" height="70%" width="70%" alt="test connection"/> </p>
</p>
<br />

<p>
We will now install Active Directory on the Domain Controller. On DC-1 click start and search for "Server Manager". Select "Add roles and features" in the Server Manager Dashboard and click Next until you reach Server Roles.
<p align="center"><img src="https://i.imgur.com/K1fXiYe.png" height="70%" width="70%" alt="install active directory"/> </p>
<p align="center"><img src="https://i.imgur.com/UQw8eXk.png" height="70%" width="70%" alt="install active directory"/> </p>
</p>
<br />

<p>
Select "Active Directory Domain Services" and add features.
<p align="center"><img src="https://i.imgur.com/x0XSJEM.png" height="70%" width="70%" alt="install active directory"/> </p>
</p>
<br />

<p>
Now click Next through the rest of installation process and Install.
<p align="center"><img src="https://i.imgur.com/KPK2c5X.png" height="70%" width="70%" alt="install active directory"/> </p>
</p>
<br />

<p>
Navigate back to the Server Manager Dashboard and select the flag on the top right hand side. Click "Promote this server to a domain controller" to finish installing Active Directory.
<p align="center"><img src="https://i.imgur.com/yeKgrnl.png" height="70%" width="70%" alt="install active directory"/> </p>
</p>
<br />

<p>
Now in Deployment Configuration select "Add a new forest" and create a Root domain name. For this lab we will be using mydomain.com as our Root domain name.
<p align="center"><img src="https://i.imgur.com/J2uSCpn.png" height="70%" width="70%" alt="create root domain name"/> </p>
</p>
<br />

<p>
For Domain Controller Options create a Directory Service Restore Mode (DSRM) password and select Next until the Installation tab where you will Install. Remote Desktop will restart after. 
<p align="center"><img src="https://i.imgur.com/hOvezLJ.png" height="70%" width="70%" alt="create root domain name"/> </p>
<p align="center"><img src="https://i.imgur.com/KgFW1qY.png" height="70%" width="70%" alt="create root domain name"/> </p>
</p>
<br />

<p>
To log back into DC-1 we now have to use the Fully Qualified Domain Name (FQDN). For us this would be mydomain.com\labuser.
<p align="center"><img src="https://i.imgur.com/Y6759ii.png" height="50%" width="50%" alt="root domain name"/> </p>
</p>
<br />

<p>
In DC-1, go to the start menu and open "Active Directory Users and Computers". Right click the domain and select New -> Organizational Unit. Name this Unit "_EMPLOYEES" and create another one named "_ADMINS".
<p align="center"><img src="https://i.imgur.com/KV2opLI.png" height="70%" width="70%" alt="create organizational units"/> </p>
<p align="center"><img src="https://i.imgur.com/RjIfJzC.png" height="70%" width="70%" alt="create organizational units"/> </p>
<p align="center"><img src="https://i.imgur.com/lIvJaMp.png" height="70%" width="70%" alt="create organizational units"/> </p>
</p>
<br />

<p>
In the "_ADMINS" Organizational Unit we can create an account. Right-click in the folder and select New -> User. Fill out the Users account with the information I use below. Make sure to use a password you will remember and uncheck every box.
<p align="center"><img src="https://i.imgur.com/0oIhhtm.png" height="50%" width="50%" alt="create account"/> </p>
<p align="center"><img src="https://i.imgur.com/Ld38TYZ.png" height="50%" width="50%" alt="create account"/> </p>
</p>
<br />

<p>
To grant admin privileges to our new user, follow these steps: Right-click on the account and choose Properties. Navigate to the "Member Of" tab and click on Add. Enter "domain admins" and click on "Check Names". Next, you will see the default security group. Simply select it, click apply, and then click ok to complete the process.
<p align="center"><img src="https://i.imgur.com/ZQPuoUo.png" height="70%" width="70%" alt="grant admin privileges"/> </p>
<p align="center"><img src="https://i.imgur.com/dhmGRa2.png" height="40%" width="40%" alt="grant admin privileges"/> </p>
<p align="center"><img src="https://i.imgur.com/kAtD9eo.png" height="70%" width="70%" alt="grant admin privileges"/> </p>
</p>
<br />

<p>
Log out of DC-1 and log back in under the new admin account.
<p align="center"><img src="https://i.imgur.com/uzFhnaz.png" height="40%" width="40%" alt="log into admin account"/> </p>
</p>
<br />

<p>
Let's proceed with connecting Client-1 to the domain by following these steps: Locate Client-1 within the Azure Portal and navigate to the Networking Settings tab. Choose the virtual NIC associated with Client-1 and access the DNS Servers section. Opt for the custom option and input the private IP address of DC-1 as the designated DNS server.
<p align="center"><img src="https://i.imgur.com/8lS4FLb.png" height="70%" width="70%" alt="connect client 1 to domain"/> </p>
<p align="center"><img src="https://i.imgur.com/LxOWk5V.png" height="70%" width="70%" alt="connect client 1 to domain"/> </p>
<p align="center"><img src="https://i.imgur.com/Aqoju0W.png" height="70%" width="70%" alt="connect client 1 to domain"/> </p>
</p>
<br />

<p>
Restart Client-1 from the Azure Portal and login as our original labuser. We can now add Client-1 to the domain. Go to system -> "Rename this PC" -> Change -> Domain and type in the domain. Additionally, a tab will prompt us to login with an account that has permissions to join the domain. Use the admin account previously created.
<p align="center"><img src="https://i.imgur.com/TRr6nFC.png" height="70%" width="70%" alt="connect client 1 to domain"/> </p>
<p align="center"><img src="https://i.imgur.com/3Z2McEl.png" height="40%" width="40%" alt="connect client 1 to domain"/> </p>
<p align="center"><img src="https://i.imgur.com/awcES6A.png" height="40%" width="40%" alt="connect client 1 to domain"/> </p>
<p align="center"><img src="https://i.imgur.com/3UhTwmH.png" height="40%" width="40%" alt="connect client 1 to domain"/> </p>
</p>
<br />

<p>
Log back in using the admin account. Currently, only Administrators have permission to access Client-1. We will modify this setting to allow all domain users to log in.
<p align="center"><img src="https://i.imgur.com/2aAJX7e.png" height="50%" width="50%" alt="log into admin account"/> </p>
</p>
<br />

<p>
Right-click on the start menu and navigate to system -> remote desktop. Choose "Select users that can remotely access this PC" and click on Add. Type "domain users" and select "Check Names", then click OK. This will grant access to all domain users, allowing them to log into Client-1 remotely.
<p align="center"><img src="https://i.imgur.com/WMxGom4.png" height="70%" width="70%" alt="allow domain users"/> </p>
<p align="center"><img src="https://i.imgur.com/FntMTR7.png" height="70%" width="70%" alt="allow domain users"/> </p>
<p align="center"><img src="https://i.imgur.com/03AeZKx.png" height="50%" width="50%" alt="allow domain users"/> </p>
<p align="center"><img src="https://i.imgur.com/1hYVQu4.png" height="50%" width="50%" alt="allow domain users"/> </p>
</p>
<br />

<p>
We will now create additional users to put into our _EMPLOYEES Organizational Unit in DC-1. Login to DC-1 under the admin account and open Powershell_ise as an administrator. Create a new File and paste the following script into it:
</p>
<p>
<details>  
  <summary> <h6>Powershell Script</h6> </summary>
  
  
```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}
```
  
</details>
<p align="center"><img src="https://i.imgur.com/fGfHbeM.png" height="70%" width="70%" alt="create random users"/> </p>
<p align="center"><img src="https://i.imgur.com/WfdL7GI.png" height="70%" width="70%" alt="create random users"/> </p>
</p>
<br />

<p>
This script will create 10,000 random users with the password "Password1" in the _EMPLOYEES OU after selecting Run Script.
<p align="center"><img src="https://i.imgur.com/odfihlQ.png" height="70%" width="70%" alt="create random users"/> </p>
<p align="center"><img src="https://i.imgur.com/Bi9ID7q.png" height="70%" width="70%" alt="create random users"/> </p>
</p>
<br />

<p>
Go back to Active Directory Users and Computers and open the _EMPLOYEES OU to view the users the script has created. Select a User and log into Client-1 with it.
<p align="center"><img src="https://i.imgur.com/NlFMu0m.png" height="70%" width="70%" alt="log into random user"/> </p>
<p align="center"><img src="https://i.imgur.com/9p7QquS.png" height="70%" width="70%" alt="log into random user"/> </p>
<p align="center"><img src="https://i.imgur.com/szZbsCr.png" height="40%" width="40%" alt="log into random user"/> </p>
</p>
<br />

<p>
Congratulations on completing the lab! We have successfully deployed Active Directory and created new users!
</p>
<br />
