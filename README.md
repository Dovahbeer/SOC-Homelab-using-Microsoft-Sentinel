# SOC Homelab
### STATUS: 🔴 In Progress, had to restart on 14/6 due to incorrect guidelines... 😔
### Disclaimer
As this is one of my earlier projects there will be parts that addresses even the most basic fundemental concepts. This ensures that I've managed to adequately understand and explain each step of the process for myself and hopefully clearly enough for you!

### Concept
<img width="1877" height="1038" alt="concept" src="https://github.com/user-attachments/assets/c44d845b-cad3-4b5c-8769-1b61b9a778e3" />

The goal is to set up a Windows VM and disable firewalls. When attackers attempt to RDP into it we'll receieve failed login attempts, which will then be sent to Microsoft Sentinel and be available for analysis. 

# Step 1 - Setting up Microsoft Azure.
Thankfully Microsoft allows users to explore Azure for free, meaning that we'll only need to provide a Microsoft account to get started.

<img width="858" height="452" alt="Confidential" src="https://github.com/user-attachments/assets/270f7e23-e413-4818-b414-6dcd1df06c76" />

After providing credentials we're given *200*$ worth of credit, allowing us to proceed with the following steps.

📝*Note: If you recieve the message "You are not eligible for an Azure subscription", you can resolve this issue by creating a new account even if the credentials matches the address, phone number and credit card of the illegible account*

# Step 2 - Configuring the Virtual Machine.
The goal is to set up a Virtual Machine that will act as honeypot. This requires both creating a new Virtual Machine and lowering the security measures which makes it enticing for real hackers to attempt to infiltrate. These attempts will later on generate logs for us to analyze.
We need to create the neccesary structure in Azure

### Resource Group


# Step 3 - Setting up log transmission to Sentinel.

# Step 4 - Analyzing a few logs.

# What I've learned. 📝
