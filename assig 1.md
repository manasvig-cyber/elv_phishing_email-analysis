 Objective: Learn to discover open ports on devices in your local network to understand network exposure. 

 Tools used:Nmap and Wireshark
 Usage:
 Nmap or network mapper is a tool that alows us to do active scanning on a network in order to gain information of the ports and the active vulerabilities present 

 Wireshark: It is a tool that captures the packets,services running in the network without leaving any trace and does passive scanning.

 Step 1:
 Identify your ip address by running the command ifconfig in kali linux or ipconfig in windows terminal

 and check the netork you are on and choose the private ip address of your device

C:\Users\manas>ipconfig

Windows IP Configuration
Ethernet adapter Ethernet 2:
   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::72f5:b3e0:4996:aa78%4
   IPv4 Address. . . . . . . . . . . : 192.168.56.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :

Unknown adapter OpenVPN TAP-Windows6:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Unknown adapter OpenVPN Data Channel Offload:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Wireless LAN adapter Local Area Connection* 1:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Wireless LAN adapter Local Area Connection* 2:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Wireless LAN adapter WiFi:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::eacd:4338:d21c:eab1%23
   IPv4 Address. . . . . . . . . . . : 192.168.8.10
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.8.1

Ethernet adapter Bluetooth Network Connection:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Ethernet adapter Ethernet:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :



 Step 2:
 Run the nmap command on your ip address to establish a TCP-SYN connection 
 COMMAND:
 nmap -sS <192.168.1.0/24> 

I got all the active ports which are open and it performed a full scan and i got the names of all the ports and services which are open 

C:\Users\manas>nmap -sS 192.168.56.1/24
Starting Nmap 7.96 ( https://nmap.org ) at 2025-10-20 10:22 India Standard Time
Nmap scan report for 192.168.56.1
Host is up (0.00021s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
7070/tcp open  realserver

Nmap done: 256 IP addresses (1 host up) scanned in 65.57 seconds

STEP 3:
I analyzed the packets and the TCP-SYN running in wireshark and i could identify the flow of these ports running 

STEP 4:
Researching these ports, working and potential vulnerability they possess

Network Port Analysis: Comprehensive Functionality and Security Overview
Port 135/TCP - Microsoft RPC (MSRPC)

Primary Function:

Port 135 serves as the RPC Endpoint Mapper for Windows systems. The Microsoft Remote Procedure Call (RPC) protocol enables client-server communication, allowing programs on one computer to execute procedures on another computer over a network. This port acts as a directory service that helps locate other RPC services running on the system.

When a client needs to connect to an RPC service, it first contacts the RPC Endpoint Mapper on port 135 to discover which dynamic port (typically in the range 49152-65535) the target service is using. Think of it as a receptionist directing calls to the appropriate department extension.

Common Uses:
- Windows Active Directory operations
- Distributed File System (DFS)
- Windows Management Instrumentation (WMI)
- Print Spooler Service
- Remote Desktop Protocol coordination
- DCOM (Distributed Component Object Model) communications

Security Implications:

Port 135 is highly vulnerable and frequently targeted by attackers:
- Exploited by the Blaster worm in the early 2000s
- Used in WannaCry ransomware propagation
- Enables unauthorized remote command execution
- Vulnerable to denial-of-service (DoS) attacks
- Allows privilege escalation and lateral network movement
- Should never be exposed to the internet

Port 139/TCP (NetBIOS Session Service) and Port 445/TCP (Microsoft-DS SMB) are related but serve different roles in Windows networking.

NetBIOS is an Application Programming Interface (API) that provides three services—Name service (for name registration and resolution), Datagram distribution service (connectionless communication), and Session service (connection-oriented communication) over local networks. NetBIOS traditionally works using NetBIOS over TCP/IP (NBT), supporting legacy Windows networking with names rather than IP addresses. Port 139 is used specifically for the NetBIOS Session Service layer, facilitating SMB communications over NetBIOS.

SMB (Server Message Block) is the actual protocol for file and printer sharing, network browsing, and inter-process communication. Early SMB implementations ran on top of NetBIOS, requiring the NetBIOS Session Service (on port 139) for communication. Modern SMB versions, however, can run directly over TCP/IP without the NetBIOS layer, using port 445 to provide a more efficient and direct transport mechanism.[2][7][8]

In summary:
- Port 139 supports SMB over NetBIOS (legacy, older systems).
- Port 445 supports SMB directly over TCP/IP (modern Windows environments).
- SMB is an upper-layer protocol that can run on NetBIOS or directly on TCP/IP.
- The modern shift to port 445 removes the dependency on NetBIOS, improving efficiency and security.

Port 445/TCP - Microsoft Directory Services (Microsoft-DS)

Primary Function:
Port 445 is the modern SMB protocol port that enables direct hosting of SMB over TCP/IP without requiring NetBIOS. This is the primary port for Windows file sharing in contemporary networks.

How It Works:
Unlike port 139, port 445 allows SMB to operate directly over TCP/IP, bypassing the older NetBIOS layer entirely. This provides more efficient and streamlined communication between Windows systems.

Core Capabilities:
- File sharing and network browsing
- Printer sharing across networks
- Active Directory operations
- Remote administration
- Named pipes for inter-process communication
- Support for multiple SMB versions (1.0, 2.0, 3.0+) with negotiated encryption

Security Implications:
Port 445 is extremely critical from a security perspective:
- Primary attack vector for WannaCry and NotPetya ransomware
- Exploited via EternalBlue vulnerability (SMBv1 flaw)
- Used by TrickBot and Emotet malware for lateral movement
- Enables unauthorized file access if misconfigured
- Allows remote code execution through SMB vulnerabilities
- Must be properly secured with firewall rules and SMB signing

Port 7070/TCP - RealServer / ARCP

Primary Function:
Port 7070 is historically associated with RealAudio/RealVideo streaming and RTSP (Real Time Streaming Protocol). It is also registered with IANA as ARCP (Automatic Router Configuration Protocol).

How It Works:
This port facilitates streaming media communication, where clients initiate conversations with media servers for authentication and playback control. TCP port 7070 handles the control channel for streaming sessions, managing commands like play, pause, and stop.

Modern Uses:
- Apple QuickTime Streaming Server (RTSP alternate)
- AnyDesk remote desktop software (direct line connection)
- RealServer streaming media services
- Avaya IP Office web socket communications
- LiveUpdate Administrator (Symantec) web console

Security Implications:
Port 7070 has several documented vulnerabilities:
- RealServer DoS vulnerability (CVE-2000-0272): Malformed input can crash the server
- Apple QuickTime RTSP exploit (CVE-2003-0054): Remote code execution via log file injection
- Lack of authentication in certain implementations (e.g., KERUI cameras)
- Information disclosure risks in Avaya IP Office implementations
- Generally considered lower risk than ports 135/139/445, but should still be firewalled from public access

Security Recommendations:
Given these open ports on your system, here are critical security measures:
- Restrict Access: Block ports 135, 139, and 445 from internet exposure using firewall rules
- Use VLANs: Segment network traffic to limit attack surface
- Enable SMB Signing: Prevent man-in-the-middle attacks on SMB traffic
- Disable SMBv1: Remove legacy protocol support to prevent EternalBlue-type exploits
- Implement Authentication: Use strong authentication mechanisms for all RPC services
- Monitor Port Usage: Regularly audit which services are listening on these ports
- Apply Patches: Keep Windows systems updated with latest security patches
- Consider Port 7070: Evaluate if RTSP/streaming services are necessary; close if unused

These ports collectively represent significant components of Windows networking infrastructure, but they also constitute major attack vectors when improperly secured. For cybersecurity practice and reconnaissance scenarios, understanding these ports is essential for both offensive security assessments and defensive hardening strategies.