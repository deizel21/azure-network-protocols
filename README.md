# azure-network-protocols
<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Creating the Resource Group
- Observing ICMP traffic in Wireshark
- Configuring Network Security Group
- Step 4

<h2>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h2>

•	Create A resource Group
![image](https://github.com/user-attachments/assets/2ad0eb33-fec5-4d8a-a9b3-a636f0e5e20e)

•	Next search for Virtual machine and create a Virtual Machine
•	Then Select the Resource Group you previously created
•	Next name the Virtual Machine Windows-VM 

![image](https://github.com/user-attachments/assets/dae2e83b-15c3-4189-983f-1d7c3d87ee27)
•	IMPORTANT -Make sure the image for the Virtual Machine is Windows 10 Pro 

![image](https://github.com/user-attachments/assets/e9f66618-903d-4c5a-9cc6-7eb259f6f0cb)
•	Create a Username and Password.
•	Then press next. Then press next to pass the Disk Section until you get to Networking

![image](https://github.com/user-attachments/assets/9863c290-b7f8-4c00-a5f9-4a5222b9ddb5)
•	Then by Virtual Network, Press create new

![image](https://github.com/user-attachments/assets/46efa3d8-4a5f-4f53-90dd-3e0163bf21a6)
•	Name your VM anything you want with a -vnet 

![image](https://github.com/user-attachments/assets/60e0ad3c-d501-4449-92a9-2b16468e117f)
•	IMPORTANT – Make sure you select the Licensing button on the Basic Section!

![image](https://github.com/user-attachments/assets/14b81240-35d5-4e75-9077-cd79d0a6cb2a)
•	Review and create so it looks something like this. This is just basic testing .

![image](https://github.com/user-attachments/assets/eb974b45-3753-483b-8100-18964419670c)

•	Once the VM is created, go back to Resource Group and look at the VM information  that has been created.
•	Next we will create a Linux VM (Ubuntu) within the same network and has the same Resource Group Name.
•	Under the Basic Tab, Change the name to Linux-VM > Select US East> Image is Ubuntu Server 22
•	Size should be this : Standard D2s v3 (2 vcpus, 8 GiB memory)
•	Make sure the Virtual network name is the same as the Windows VM since we need this VM to be under the same network. 
•	Then Review and Create

![image](https://github.com/user-attachments/assets/3effbf64-490b-4d8a-aed3-3afc09f0c945)
![image](https://github.com/user-attachments/assets/f5b3ac6e-51b3-484d-b8a8-0c7103eb79f7)
![image](https://github.com/user-attachments/assets/f44b08e1-db06-42f1-83e8-191c5a31bf5f)

•	When trying to connect to the VM, Select the Native RDP and Download the RDP file. Then connect with the username and password you made for the VM. 

![image](https://github.com/user-attachments/assets/59c7fac0-ee67-4a4d-9e89-475b664ed050)
![image](https://github.com/user-attachments/assets/6cf738f6-fabe-4b87-ad62-fe1c0110bd8c)

•	Press the Ethernet button to get into this menu above
•	Then type in icmp to filter only ICMP traffic.
![image](https://github.com/user-attachments/assets/e1544554-f6cf-4e96-9f5b-037d076b7ac0)

•	Grab the Private IP address from the Linux VM and ping the IP address within the Windows VM using PowerShell.
![image](https://github.com/user-attachments/assets/35263459-899b-4ed5-a918-398b881dfbc2)

•	Commands in Powershell: ping (private ip address), ping (private ip address) -t  The -t means it will constantly ping each time/infinitely 

![image](https://github.com/user-attachments/assets/e36dcea9-44d6-4e1f-88b3-c72f32d6fccc)
![image](https://github.com/user-attachments/assets/dbab88b0-116c-41eb-8405-6aa17f8f0831)

•	We Will configure a Network Security Group to block all incoming ping traffic from the Windows VM

![image](https://github.com/user-attachments/assets/f8a4a823-444c-4844-87e0-fd074f3f95f7)

•	Under Destination port ranges put an * instead of any. ICMP doesn’t have a port #.  Also, for protocol put ICMPv4. Action> Deny. Priority will be set to 290 since that rule will be read 1st.

![image](https://github.com/user-attachments/assets/d7d1c4e5-8039-44b5-a05b-0a878b87a14b)
![image](https://github.com/user-attachments/assets/f57d1c55-973e-4928-9d1f-017180b95438)

•	You should see a “ Request timed out” in PowerShell once the rule is in effect.
•	Delete the NSG rule we just created and observe PowerShell to see the ping Replies coming back
•	Press Ctrl C to return to stop seeing the replies.
•	Next we will ssh or remote into the Linux VM from the Windows VM.
•	Type ssh labuser1@<private ip of linux vm>

![image](https://github.com/user-attachments/assets/d577433c-b0b6-4d3f-af34-49ea0fc665e4)

•	Should see this after remoting in.
![image](https://github.com/user-attachments/assets/6504a79c-bc96-4996-8f93-00164eeeaa01)

•	You can restart the packet filtering. Then type in ssh or tcp.port == 22 
•	This will show every character that’s being typed on the Linux VM in Wireshark
•	You should see a reset packet in Wireshark once you type exit in the PowerShell Terminal. 

![image](https://github.com/user-attachments/assets/5de1b14c-3e20-42e9-948c-106017a81147)

•	Next we will do some testing to see DHCP traffic in Wireshark
•	In Powershell do ipconfig /renew. 
•	IMPORTANT Don’t do ipconfig /release FIRST. You will break the VM and must restart the VM over!


![image](https://github.com/user-attachments/assets/6e6de6c5-72bc-49fd-88c2-280e5a091ac3)
•	Filtering traffic with DNS using nslookup command on Powershell
![image](https://github.com/user-attachments/assets/6162e868-1314-4495-89a4-926cee75859f)

•	Type RDP or tcp.port == 3389 in the Wireshark search

•	Overall Steps
LAB SUMMARY
Part 1
https://portal.azure.com/
(Create our Virtual Machines)
1.	Create a Resource Group
2.	Create a Windows 10 Virtual Machine (VM)
a.	While creating the VM, select the previously created Resource Group
b.	While creating the VM, allow it to create a new Virtual Network (Vnet) and Subnet
3.	Create a Linux (Ubuntu) VM
a.	While creating the VM, select the previously created Resource Group and Virtual Network—the Virtual Network MUST BE THE SAME.
b.	Authentication type: Username/Password
4.	Ensure both VMs are in the same Virtual Network / Subnet
5.	End the lab, but keep both VMs for Part 2!

Part 2
https://portal.azure.com/
(Observe ICMP Traffic)
6.	If using Mac, install Microsoft Remote Desktop
7.	Use Remote Desktop to connect to your Windows 10 Virtual Machine
8.	Within your Windows 10 Virtual Machine, Install Wireshark
9.	Open Wireshark and start packet capture
10.	Within Wireshark, filter for ICMP traffic only
11.	Retrieve the private IP address of the Ubuntu VM (linux-vm) and attempt to ping it from within the Windows 10 VM
a.	Observe ping requests and replies within WireShark
12.	From The Windows 10 VM, open command line or PowerShell and attempt to ping a public website (such as www.google.com) and observe the traffic in WireShark

Part 3
(Configuring a Firewall [Network Security Group])
13.	Initiate a perpetual/non-stop ping from your Windows 10 VM to your Ubuntu VM
a.	Open the Network Security Group your Ubuntu VM is using and disable incoming (inbound) ICMP traffic
b.	Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity
c.	Re-enable ICMP traffic for the Network Security Group your Ubuntu VM is
d.	Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity (should start working)
e.	Stop the ping activity

(Observe SSH Traffic)
14.	Log back into the windows-vm
15.	Back in Wireshark, start a packet capture up
16.	Filter for SSH traffic only
17.	From your Windows 10 VM, “SSH into” your Ubuntu Virtual Machine (via its private IP address)
18.	Open PowerShell, and type: ssh labuser@<private IP address>
a.	Type commands (username, pwd, etc) into the linux SSH connection and observe SSH traffic spam in WireShark
b.	Exit the SSH connection by typing ‘exit’ and pressing [Enter]

(Observe DHCP Traffic)
19.	Back in Wireshark, filter for DHCP traffic only
20.	From your Windows 10 VM, attempt to issue your VM a new IP address from the command line
a.	Open PowerShell as admin and run: ipconfig /renew
b.	Observe the DHCP traffic appearing in WireShark

Observe DNS Traffic)
21.	Back in Wireshark, filter for DNS traffic only
22.	From your Windows 10 VM within a command line, use nslookup to see what google.com and disney.com’s IP addresses are
a.	Observe the DNS traffic being show in WireShark

(Observe RDP Traffic)
23.	Back in Wireshark, filter for RDP traffic only (tcp.port == 3389)
24.	Observe the immediate non-stop spam of traffic? Why do you think it’s non-stop spamming vs only showing traffic when you do an activity?
a.	Answer: because the RDP (protocol) is constantly showing you a live stream from one computer to another, therefor traffic is always being transmitted

Lab Cleanup (DON’T FORGET THIS)
25.	Close your Remote Desktop connection
26.	Delete the Resource Group(s) created at the beginning of this lab
27.	Verify Resource Group Deletion




This is the end of the Lab

