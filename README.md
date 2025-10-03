                    Project 1:   Network Intrusion Detection System (NIDS)Rule Creation and Testing Lab 🕵️‍♂️

              

Problem Statement: Develop and test a robust set of custom rules for a Network Intrusion Detection System (NIDS) to identify and flag common cyber-attacks in real-time, reducing the mean time to detect threats within a network. 

Tools & Technologies Used: 
• NIDS Engine: Snort, 
• Operating System: Kali Linux, Ubuntu Server 
• Virtualization: VirtualBox
• Attack & Testing Tools: Hydra 
• Scripting & Analysis: Bash 


Focus Directory are
	Ubuntu Server
		cybermonk@myLap:~ $ cd /etc/snort/rules/local.rules
		cybermonk@myLap:~ $ cd /var/log/snort



This guide details how to set up Snort, a Network Intrusion Detection System (NIDS), to detect an SSH brute-force attack. 


Step 1: Setup and Installation 
1. Install Ubuntu Server: Use VirtualBox or VMware to create a new virtual machine. Install a minimal Ubuntu Server. Ensure the network adapter is set to "Bridged Mode" to get an IP address from your local network. 

2. Install Snort: Once the VM is running, update your package list and install Snort. 

cybermonk@myLap:~ $sudo apt update 
cybermonk@myLap:~ $sudo apt install -y snort 

3. Configure Network Interface: During installation, you'll be prompted for the network interface to monitor. Enter the name of your primary interface (e.g., eth0 or enp0s3). You can find it by running the ip a command. Also, provide your local network range in CIDR notation (e.g., 192.168.0.0/24). 





Step 2: Create a Custom NIDS Rule 
1. Open the Rules File: Snort's custom rules can be placed in /etc/snort/rules/local.rules. Open this file with a text editor like nano. 

cybermonk@myLap:~ $sudo vim /etc/snort/rules/local.rules 

2. Add a Brute-Force Rule: Add the following rule to the bottom of the file. This rule alerts if it sees more than 5 connection attempts to the SSH port (22) from the same source IP within 60 seconds. 

alert tcp any any -> $HOME_NET 22 (msg:"SSH Brute-Force Attempt Detected"; flow:to_server,established; detection_filter:track by_src, count 5, seconds 60; sid:1000002; rev:1;)

Meaning in plain English:
“Raise an alert if any external host makes 5 or more SSH (TCP/22) connection attempts to my home network within 60 seconds, as part of an established session.”Meaning in plain English:
“Raise an alert if any external host makes 5 or more SSH (TCP/22) connection attempts to my home network within 60 seconds, as part of an established session.”


Step 3: Test the Rule  

1. Start Snort: Run Snort in console mode to watch for alerts in real-time. Replace enp0s3 with your network interface. 

cybermonk@myLap:~ $sudo snort -A console -q -c /etc/snort/snort.conf -i enp0s8 



2. Install an SSH Server: To attack something, you need an SSH server running on your Snort VM. 

kali@kali:~$sudo apt install -y openssh-server 



3. Perform the Attack: From another machine on the same network (your host machine or another VM), use a tool like Hydra to simulate a brute-force attack. You'll need a dummy password list. 

# Create a small password list 
kali@kali:~$echo "password123\nadmin\nroot\n123456\nqwerty" > pass.txt 




# Run Hydra (replace <VM_IP> with the Ubuntu VM's IP address) 

kali@kali:~$hydra -l non_existent_user -P pass.txt ssh://<VM_IP> 
so,
kali@kali:~$hydra -l non_existent_user -P pass.txt ssh://192.168.56.102


4. Verify the Alert: Watch the console where Snort is running. After a few seconds of the Hydra attack, you will see the alert message "SSH Brute-Force Attempt Detected" appear multiple times. 





cybermonk@myLap:~ $ sudo snort -A console -q -c /etc/snort/snort.conf -i enp0s8
10/03-21:41:35.967629  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48994 -> 192.168.56.102:22
10/03-21:41:35.967313  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48988 -> 192.168.56.102:22
10/03-21:41:35.990411  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48960 -> 192.168.56.102:22
10/03-21:41:35.988153  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48966 -> 192.168.56.102:22
10/03-21:41:36.008844  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48972 -> 192.168.56.102:22
10/03-21:41:36.012344  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48994 -> 192.168.56.102:22
10/03-21:41:36.013830  [**] [1:1000002:1] SSH Brute-Force Attempt Detected [**] [Priority: 0] {TCP} 192.168.56.104:48988 -> 192.168.56.102:22

To View in the server Log 
cybermonk@myLap:.../log/snort $ ls
snort.alert snort.alert.fast  snort.log  snort.log.1759238389  snort.log.1759527594


Scripting & Analysis: 
Since the file is not in a Human Readable so to be converted into a human-readable format
cybermonk@myLap:~ $ sudo snort -r /var/log/snort/snort.log.1759527594 &> /home/cybermonk/snort-log.txt
 



Reference 

https://medium.com/@huglertomgaw/snort-tryhackme-fab9838b715b


