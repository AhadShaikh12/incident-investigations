Incident Investigation — Incident 001

A hands-on SOC investigation of a simulated corporate security incident using Splunk log analysis, authentication monitoring, file-access analysis, network activity, and IOC investigation.

This project demonstrates how a SOC analyst can investigate suspicious activity, build a timeline, identify indicators, and document findings.

🎯 Investigation Objective

The objective of this investigation was to analyze suspicious activity involving the user account john.smith and determine:

Whether the authentication activity was suspicious
Whether the account was successfully compromised
What files were accessed
What network activity occurred
Which IP addresses were involved
Whether the available evidence can identify the actual attacker
🛠️ Tools & Technologies
Splunk — Log ingestion and SIEM investigation
Kali Linux — Security investigation environment
Nmap — Network investigation
Wireshark — Network traffic analysis
VirusTotal — IOC/file reputation checking
PEStudio — Windows executable analysis
YARA — Malware pattern detection
WHOIS / IP investigation — IP and infrastructure analysis
📊 Incident Overview

The investigation contained 34 events.

The main suspicious activity involved:

User: john.smith
Internal Source IP: 10.10.20.45
Target: SERVER01 / FILESRV01
Activity: Authentication → File Access → Network Connections → Process Execution

🔎 Key Findings
1. Multiple Failed Login Attempts

The account experienced five consecutive failed login attempts from the same internal IP address.

08:41:12  LOGIN_FAIL
08:41:18  LOGIN_FAIL
08:41:25  LOGIN_FAIL
08:41:31  LOGIN_FAIL
08:41:39  LOGIN_FAIL
08:41:47  LOGIN_SUCCESS

This pattern is suspicious because a successful login occurred immediately after repeated authentication failures.

2. Successful Authentication

After the failed attempts, the account successfully authenticated from:

10.10.20.45

A session was subsequently created for the account.

3. Sensitive File Access

After authentication, the account accessed multiple files in the HR shared directory, including:

Payroll.xlsx
Employees.xlsx
Salaries.xlsx
BankDetails.xlsx

This activity increased the severity of the investigation because the files contain potentially sensitive business information.

4. Network Activity

The internal host communicated with:

198.51.100.27

over HTTPS/443.

The address is part of a documentation/test network range used in the simulated dataset, so it should not be treated as a real attacker infrastructure address.

5. Suspicious Process Activity

The workstation generated the following process chain:

explorer.exe
    ↓
powershell.exe
    ↓
cmd.exe
    ↓
certutil.exe

Network activity was also associated with certutil.exe.

A separate rundll32.exe process also established a network connection.

These events are important for further endpoint investigation.

🕐 Investigation Timeline
Time	Activity
08:41	Multiple failed logins
08:41	Successful login
08:42	Session created
08:43–08:44	HR files accessed
08:44–08:45	SMB network activity
08:46	DNS queries
08:47	External HTTPS connections
08:48	User logout
08:49	Administrator login attempts
08:50	RDP-related network activity
08:51	PowerShell → CMD → Certutil
08:52	Rundll32 network connection
08:53	BankDetails.xlsx accessed
08:54	External HTTPS connection
08:55	Final logout
🌐 IP Address Analysis
Internal IP
10.10.20.45

This is a private IPv4 address from the RFC1918 10.0.0.0/8 range.

It identifies an internal host/network address and is not directly routable on the public Internet.

External/Test IP
198.51.100.27

This address belongs to the TEST-NET-2 documentation range and is therefore treated as simulated infrastructure in this lab.

⚠️ Attribution Limitation

The available logs do not prove that John Smith himself was the attacker.

The evidence establishes that suspicious activity occurred using the john.smith account and the associated internal source IP.

Possible explanations include:

Compromised credentials
A compromised workstation
Session hijacking
Another person using the workstation
Malicious activity performed through the user's account

Additional evidence such as Windows Security logs, Sysmon, EDR telemetry, authentication records, process command lines, and endpoint ownership would be required for stronger attribution.
