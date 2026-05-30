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

1.2 Installation Steps
Run the installer on the Windows (victim) machine
Accept the license agreement
Set a username and password

These credentials are used later for troubleshooting and configuration.

1.3 Forwarding Configuration
Configure receiving indexer IP: 192.168.65.140
Use default port: 9997

Deployment Server configuration is optional for this lab.

1.4 Post Installation Configuration

Navigate to:

C:\Program Files\SplunkUniversalForwarder\etc\system\local
Create or edit inputs.conf
Configure log forwarding

⚠️ Ensure index name matches the index created in Splunk (Kali)

# 🖥️ 2. Splunk Indexer Setup (Kali Linux)
2.1 Install Splunk

Download and install Splunk on Kali Linux.

2.2 Access Splunk Web GUI
Open browser and access Splunk
Login with configured credentials (e.g., topu)

Splunk service should be running properly.

2.3 Create Index

Navigate:

Settings → Indexes
Create a new index
Ensure name matches inputs.conf

2.4 Configure Receiving Port
Go to Forwarding and Receiving
Click New Receiving Port
Add port: 9997
Set status: Enabled
# 🔍 3. Sysmon Installation (Windows)
3.1 Download Sysmon
Download Sysmon executable
Extract files

3.2 Apply Configuration
Download Sysmon XML config
Place XML in same directory

3.3 Install Sysmon

Install using configuration file:

sysmon64.exe -accepteula -i sysmonconfig.xml
# 🔄 4. Forwarder Validation
4.1 Check Forwarding Status

Run:

splunk list forward-server

This confirms:

Active forwarder
Destination (Kali IP)
Port 9997
4.2 Verify Logs in Splunk
Open Splunk GUI (Kali)
Search logs

Logs should appear under:

index=wineventlog
# ⚔️ 5. Atomic Red Team (ART) Setup
5.1 Pre-requisite

Disable Windows Defender real-time protection
(This prevents scripts from being blocked)

5.2 Install Atomic Red Team

Run:

IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1')

Download attack definitions:

Install-AtomicRedTeam -getAtomics

5.3 PowerShell Configuration
Enable TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install package manager
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force

Trust repository
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

5.4 Install Required Modules
Install-Module PowerShellGet -Force -AllowClobber
Install-Module Invoke-AtomicRedTeam -Force
Import-Module Invoke-AtomicRedTeam
Set-ExecutionPolicy Bypass -Scope Process -Force

5.5 Set Atomic Path
$env:PathToAtomicsFolder="C:\AtomicRedTeam\atomics"

5.6 Validate Installation
Get-Command Invoke-AtomicTest

Expected version: 2.3.0

5.7 Install Additional Modules
Install-Module -Name AtomicTestHarnesses -Scope CurrentUser -Force
Import-Module AtomicTestHarnesses

# 📊 6. Log Monitoring in Splunk

Once setup is complete:

Sysmon logs will be forwarded to Splunk
Logs can be searched and analyzed

Example search:

index=wineventlog
