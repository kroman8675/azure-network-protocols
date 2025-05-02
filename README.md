<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe different network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computer)
- Remote Desktop
- Different Command-Line Tools
- Different Network Protocols (SSH, DHCP, DNS, RDP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (22H2)
- Ubuntu Server 24.04

<h2> Steps</h2>

- Step 1: Configure a Network Security Group 
- Step 2: Notice the effect of the NSG
- Step 3: Delete the NSG and observe ICMP
- Step 4: Observe some other Network Protocls (SSH,DNS,RDP)

<h2>Actions and Observations</h2>

- Step 1: Configure a Network Security Group

<img width="1440" alt="Screenshot 2025-05-02 at 9 14 45 AM" src="https://github.com/user-attachments/assets/e894e6ab-6ed7-4436-b7b2-44b6b14e4279" />

On the Azure website, select the Linux VM. On the side click Networking then Networking Settings. Under Rules on the bottom right hand side of the screen, click Create a Port Rule then Inbound Port Rule.

<img width="1440" alt="Screenshot 2025-05-02 at 9 21 43 AM" src="https://github.com/user-attachments/assets/8b40d344-c0f5-4185-9d29-b17de87e3694" />

For Destination Port ranges, I put * as ICMP doesn't have a port number. For the Protcol put ICMPv4 and the Action select Deny. The priority I set to 290 so it can prioritize above the other inbound security rule, click Add.

- Step 2: Notice the effect of the NSG

<img width="952" alt="Screenshot 2025-05-02 at 9 27 43 AM" src="https://github.com/user-attachments/assets/8d87f12a-2770-4ccc-9e73-39a3c4f5d3cd" />

Log into the Windows VM, open Wireshark and double click Ethernet. Filter for ICMP traffic by typing in icmp into the Apply a display filter bar and press enter.

<img width="1440" alt="Screenshot 2025-05-02 at 9 30 34 AM" src="https://github.com/user-attachments/assets/29f261f7-a89a-4356-81df-a20858f9ba89" />

Open Windows Powershell. Type ping and the Linux VM's private IP address and hit enter. Notice how in Powershell it says Request timed out and the 4 packets were lost, that is since we set up a rule that denied incoming ICMP traffic. In Wireshark you should only see request 4 times.

- Step 3: Delete the NSG and observe ICMP

<img width="1440" alt="Screenshot 2025-05-02 at 9 38 39 AM" src="https://github.com/user-attachments/assets/b1118729-bee3-497e-9b7f-a75e67b0b574" />

Go into the Azure website if not already, select the Linux VM. On the side click Networking then Networking Settings. Under Rules on the bottom right hand side of the screen, click the trashcan and select Yes for deleting the security rule.

<img width="1440" alt="Screenshot 2025-05-02 at 9 42 44 AM" src="https://github.com/user-attachments/assets/81e59704-45be-4d0f-95e1-3f9978840cca" />

Back in the windows VM attempt to ping the Linux's private IP address again. Notice how in Poweshell now all the packets were recieved. In wireshark instead of it just request, it now show request and reply 4 times. The request is the windows VM and the Linux now responding back with Reply.

- Step 4: Observe the other Network Protocls (SSH, DNS, RDP)

<img width="1428" alt="Screenshot 2025-05-02 at 10 30 28 AM" src="https://github.com/user-attachments/assets/043af137-4cbf-4365-b64c-2318b4d00e8d" />

In Wireshark, type ssh in the filter bar and press enter. In Powershell type ssh, the username, the @ symbol,a and Linux VMs private IP adress and hit enter. When it asks, Are you sure you want to continue connecting?, type yes and press enter. Now type in your password and press enter. 

![image](https://github.com/user-attachments/assets/085b7ba6-8471-4538-90b0-d763ccf287c4)

You should now be connected to the Linux VM.I typed in Hi in Powershell and notice the traffic come in Wireshark .Click on one of the events in Wireshark and click the SSH protocol then the SSH Version 2 dropdown. The encrypted packet is part of some of the traffic, we cannot see hello or part of it as everything that is typed is sent over securely. In Powershell type exit then press enter to close out the SSH connection.

<img width="1440" alt="Screenshot 2025-05-02 at 11 07 59 AM" src="https://github.com/user-attachments/assets/fe4af3ea-f9a6-449f-9fef-9e4593164de7" />

In Wireshark in the filter bar type DNS and press enter. In Powershell type nslookup google.com and press enter. Now in Wireshark click the most recent event. If I type in google's IPv4 address that is in Powershell which is currently 142.251.215.238, in the browser and press enter, it takes me to google.com

<img width="1436" alt="Screenshot 2025-05-02 at 11 24 59 AM" src="https://github.com/user-attachments/assets/f350bb87-cf3e-4b61-87b6-e5e4b8f2d699" />

Go back to wireshark, the event should still be selected, click Domain Name System (response) dropdown, and select both the queries and answers dropdown. Notice how in the answers section it include other information that the query has like the data length, time to live, and the AAAA address which is google's current IPv6 address.

<img width="1440" alt="Screenshot 2025-05-02 at 1 18 40 PM" src="https://github.com/user-attachments/assets/a3276f38-bd24-46e8-9e5f-01313eb4f679" />

In Wireshark in the filter bar type tcp.port == 3389, which is the port number for RDP. Notice how there is a constant flow of traffic since we connect to the VM via RDP.
















