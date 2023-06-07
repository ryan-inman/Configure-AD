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
We will now install Active Directory on the Domain Controller. On DC-1 click start and search for "Server Manager". Select "Add roles and features" in the Server Manager Dashboard.
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
