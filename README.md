# Splunk-Atomic-Red-Team-ART-Lab-Setup
This is an assignment for course CSE 802: Information Security and Cryptography, Dhaka University-2026, PMICS Batch 6.

🛡️ Splunk & Atomic Red Team (ART) Lab Setup
# 📌 Overview

This project demonstrates the setup of a complete SOC (Security Operations Center) lab environment using Splunk, Sysmon, and Atomic Red Team (ART).

 # The objective is to:

Forward logs from a Windows machine to Splunk (Kali Linux)

Monitor system activity using Sysmon

Simulate attacks using Atomic Red Team

Prepare the environment for detection engineering

🏗️ Lab Architecture

Kali Linux → Splunk Indexer

Windows Server → Victim Machine + Forwarder + Sysmon + ART

# ⚙️ 1. Splunk Universal Forwarder Installation (Windows)
1.1 Download Splunk Universal Forwarder

Download Splunk Universal Forwarder (UF) from the official Splunk website.
![Sysmon Installation](images/image1.png)

Select Required one and download

![Sysmon Installation](images/image2.png)

1.2 Installation Steps

Run the installer on the Windows (victim) machine and Accept the license agreement to proceed.

![Sysmon Installation](images/image3.png)

Set a username and password

![Sysmon Installation](images/image4.png)


These credentials are used later for troubleshooting and configuration on victim UF client.

1.3 Universal Forwardar Configuration(Victim Machine)

Deployment Server configuration is optional for this lab.

![Sysmon Installation](images/image5.png)

Configure receiving indexer IP: 192.168.65.140(Kali Splunk Indexer)

Use default port: 9997

![Sysmon Installation](images/image6.png)


Then Complete the UF client installtion and configuration.

![Sysmon Installation](images/image7.png)

1.4 Post Installation Configuration

Navigate to: C:\Program Files\SplunkUniversalForwarder\etc\system\local

Create or edit inputs.conf

![Sysmon Installation](images/image8.png)

Configure log forwarding ( index = wineventlog )

⚠️ Ensure index name matches the index created in Splunk (Kali)

# 🖥️ 2. Splunk Indexer Setup (Kali Linux)
2.1 Download & Install Splunk

Download Splunk on Kali Linux.

![Sysmon Installation](images/image9.png)

Install Splunk on Kali Linux. During Installation, setup the admin creds for GUI access and Management.

![Sysmon Installation](images/image10.png)

Splunk service should be running properly.

![Sysmon Installation](images/image12.png)

2.2 Access Splunk Web GUI

Open browser and access Splunk

Login with configured credentials (e.g., topu)

![Sysmon Installation](images/image13.png)



2.3 Configure Kali Indexer from GUI

To Create Index

Navigate:

Settings → Indexes
Create a new index
Ensure name matches inputs.conf

![Sysmon Installation](images/image14.png)

This name is mentioned in step 1.4 Post Installation Configuration


2.4 Configure Receiving Port

Go to Forwarding and Receiving -> Click New Receiving Port

![Sysmon Installation](images/image15.png)

Add port: 9997

Set status: Enabled
# 🔍 3. Sysmon Installation (Windows)
3.1 Download Sysmon

Download Sysmon executable from Official Link : https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

Extract files

![Sysmon Installation](images/image16.png)

3.2 Apply Configuration

Download Sysmon XML config from here : https://wazuh.com/resources/blog/emulation-of-attack-techniques-and-detection-with-wazuh/sysmonconfig.xml

![Sysmon Installation](images/image17.png)

Place XML in same directory

![Sysmon Installation](images/image18.png)

3.3 Install Sysmon

Install using configuration(downloaded from wazuh sites) file:

Sysmon64.exe -accepteula -i sysmonconfig.xml

![Sysmon Installation](images/image19.png)

# 🔄 4. Forwarder Validation(on Victim Machine)

4.1 Check Forwarding Status

Run:

splunk list forward-server

![Sysmon Installation](images/image20.png)

This confirms:

Active forwarder

Destination (Kali IP-Indexer)

Port 9997

4.2 Verify Logs in Splunk

Open Splunk GUI (Kali)

Search logs

Logs should appear under: index=wineventlog

![Sysmon Installation](images/image21.png)

# ⚔️ 5. Atomic Red Team (ART) Setup on Victim Windows Machine
5.1 Pre-requisite

Disable Windows Defender real-time protection. To run the installation hassle free and perfectly on windows, we need to disable windows defender and stop the real time monitoring. Otherwise all the attack files will be removed/quarantine. 

![Sysmon Installation](images/image22.png)
![Sysmon Installation](images/image23.png)

(This prevents scripts from being blocked)

5.2 Install Atomic Red Team

Run on Windows Powershell:

IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1')

Download attack definitions:

Install-AtomicRedTeam -getAtomics

![Sysmon Installation](images/image24.png)
![Sysmon Installation](images/image25.png)

All the attack config file to invoke specific mitre attack. 

Files are downloaded locally to execute.


5.3 PowerShell Configuration

Need to apply below commands one by one. 

Enable TLS 1.2

[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install package manager

Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force

Avoid confirmation prompts during installs

Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted


5.4 Install Required Modules

Fix module install issues

Install-Module PowerShellGet -Force -AllowClobber

Install the main tool

Install-Module Invoke-AtomicRedTeam -Force

Load commands like Invoke-AtomicTest

Import-Module Invoke-AtomicRedTeam

Allow scripts to run, PowerShell blocks scripts by default.

Set-ExecutionPolicy Bypass -Scope Process -Force

5.5 Set Atomic Path

To set the environment path so that every time it takes the specific mitre attack from this designated path to be executed.

$env:PathToAtomicsFolder="C:\AtomicRedTeam\atomics"

5.6 Validate Installation

Get-Command Invoke-AtomicTest

![Sysmon Installation](images/image26.png)

Expected version: 2.3.0

5.7 Install Additional Modules

Install-Module -Name AtomicTestHarnesses -Scope CurrentUser -Force

Import-Module AtomicTestHarnesses

Install extra module, provides additional helpers for tests because Some tests require dependencies.

# 📊 6. Log Monitoring in Splunk

Once setup is complete:

Sysmon logs will be forwarded to Splunk
Logs can be searched and analyzed

Executing test: T1059.001 for sample mitre attack test using ART. 

![Sysmon Installation](images/image27.png)
