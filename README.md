<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
We will start by creating the Domain Controller and Client VMs. Set up the Domain Controller VM (Windows Server 2022) named "DC-1". Note the Resource Group and Virtual Network (Vnet) created during the VM setup.
</p>
<br />

<p>
Create the Client VM (Windows 10) named "Client-1" using the same Resource Group and Vnet as the Domain Controller.
</p>
<br />

<p>
Let's configure the Domain Controller's NIC Private IP address to static. This is required to allow the Client VM to join the domain. Locate DC-1 select Networking -> Network Interface -> IP configurations -> ipconfig1 and switch the Assignment to "Static".
</p>
<br />

<p>
Now we can test connectivity between the Client and Domain Controller. Log in to "Client-1" using Remote Desktop and initiate a perpetual ping (ping -t) to DC-1's private IP address. Note that the request will time out because DC-1's firewall is blocking ICMP traffic.
</p>
<br />

<p>
Now login to the Domain Controller so that we can enable ICMPv4 in the local Windows Firewall. This will allow ping requests between the Client and Domain Controller.
</p>
<br />

<p>
From DC-1's desktop click start and search "Windows Defender Firewall with Advanced Security". Select Inbound Rules and sort by Protocol for easier navigation. Now right click the two Inbound Rules named "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)" and select Enable Rule.
</p>
<br />

<p>
Observe the ping requests now working in Client-1.
</p>
<br />

<p>
We will now install Active Directory on the Domain Controller. On DC-1 click start and search for "Server Manager". Select "Add roles and features" in the Server Manager Dashboard and click Next until you reach Server Roles.
</p>
<br />

<p>
Select "Active Directory Domain Services" and add features.
</p>
<br />

<p>
Now click Next through the rest of installation process and Install.
</p>
<br />

<p>
Navigate back to the Server Manager Dashboard and select the flag on the top right hand side. Click "Promote this server to a domain controller" to finish installing Active Directory.
</p>
<br />

<p>
Now in Deployment Configuration select "Add a new forest" and create a Root domain name. For this lab we will be using mydomain.com as our Root domain name.
</p>
<br />

<p>
For Domain Controller Options create a Directory Service Restore Mode (DSRM) password and select Next until the Installation tab where you will Install. Remote Desktop will restart after. 
</p>
<br />

<p>
To log back into DC-1 we now have to use the Fully Qualified Domain Name (FQDN). For us this would be mydomain.com\labuser.
</p>
<br />

<p>
In DC-1, go to the start menu and open "Active Directory Users and Computers". Right click the domain and select New -> Organizational Unit. Name this Unit "_EMPLOYEES" and create another one named "_ADMINS".
</p>
<br />

<p>
In the "_ADMINS" Organizational Unit we can create an account. Right-click in the folder and select New -> User. Fill out the Users account with the information I use below. Make sure to use a password you will remember and uncheck every box.
</p>
<br />

<p>
To grant admin privileges to our new user, follow these steps: Right-click on the account and choose Properties. Navigate to the "Member Of" tab and click on Add. Enter "domain admins" and click on "Check Names". Next, you will see the default security group. Simply select it, click apply, and then click ok to complete the process.
</p>
<br />

<p>
Log out of DC-1 and log back in under the new admin account.
</p>
<br />

<p>
Let's proceed with connecting Client-1 to the domain by following these steps: Locate Client-1 within the Azure Portal and navigate to the Networking Settings tab. Choose the virtual NIC associated with Client-1 and access the DNS Servers section. Opt for the custom option and input the private IP address of DC-1 as the designated DNS server.
</p>
<br />

<p>
Restart Client-1 from the Azure Portal and login as our original labuser. We can now add Client-1 to the domain. Go to system -> "Rename this PC" -> Change -> Domain and type in the domain.
</p>
<br />

<p>
Now we will get a tab prompting us to login with an account that has permission to join the domain. Use the admin account previously created. Client-1 will now automatically restart.
</p>
<br />

<p>
Log back in using the admin account. Currently, only Administrators have permission to access Client-1. We will modify this setting to allow all domain users to log in.
</p>
<br />

<p>
Right-click on the start menu and navigate to system -> remote desktop. Choose "Select users that can remotely access this PC" and click on Add. Type "domain users" and select "Check Names", then click OK. This will grant access to all domain users, allowing them to log into Client-1 remotely.
</p>
<br />

<p>
We will now create additional users to put into our _EMPLOYEES Organizational Unit in DC-1. Login to DC-1 under the admin account and open Powershell_ise as an administrator. Create a new File and paste the following script into it:
</p>
<br />

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
</p>
<br />

<p>
text
</p>
<br />

<p>
text
</p>
<br />

<p>
text
</p>
<br />
