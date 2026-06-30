# SOC Homelab
### STATUS: 🔴 In Progress, 90% completed.
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
We need to create the neccesary structure in Azure in order for this lab to work. This includes ensuring that they all belong to the same subscription and subsequent resource group, virtual network etc. 

### Resource Group
First we create a Resource group, which allows us to group everything associated with this lab in one convenient spot. In a real SOC scenario this could be used to create and deploy templates, handle cost management or in this case organising labs in seperate entities.

<img width="722" height="203" alt="image" src="https://github.com/user-attachments/assets/36dcb45b-3e85-41d4-839d-0a3aa4d38349" />


### Virtual Network
To ensure that our VM will be able to communicate with other system and resources, we need to create a virtual network. This allows us to set up private ip-addresses, set up security rules and policies and allows us to configure internet access control.

<img width="750" height="374" alt="image" src="https://github.com/user-attachments/assets/5b848400-1144-420d-b980-5984663522b0" />


📝*Note: Ensure the region matches the one set in the resource group to prevent future issues*

When navigating to the Security tab, make sure that none of the boxes are checked in. We do not want protections in place as that may negatively affect the amount of attacks on our VM. In a real scenario however it's expected to configure these settings appropriately. Furthermore we'll leave the address space untouched - we could do by with less ip-addresses, but for this basic level lab it's fine to just leave as is.

<img width="700" height="274" alt="image" src="https://github.com/user-attachments/assets/9578dab3-4d32-4867-8bdd-f55c5fc5be51" />


### Virtual Machine
Our honeypot that need to be configured properly to attract attackers. At this point of the lab we're approaching closely to our free budget limit. If you're following this guide yourself, navigate in various menues to reach my configurations:

<img width="775" height="744" alt="WarningArrow" src="https://github.com/user-attachments/assets/fa4fcc99-f3c8-48d4-ba72-48fb703549da" />

‼️⚠️ **If you are following this guide, make absolutely sure you've checked in "Run with Azure Spot discount", otherwise your VM will bill you thousands of dollars per month.** ⚠️‼️

This was my final configuration, you can verify your pricing but looking at the highlighted part of the screenshot:

<img width="771" height="743" alt="PricingHighlight" src="https://github.com/user-attachments/assets/ff2bf69a-2b13-42a7-bb80-e8280395e7b8" />

When proceeding with the following tabs:

- **Network**
   - Ensure that "Virtual Network" is set to the one we created.
   - Check the box for "Delete public IP and NIC when VM is deleted"

- **Monitoring**
  - On "Boot diagnostics", check "disable".

After the VM has been reviewed and created, we can head back to our resource group and verify that everything has been successfully deployed:

<img width="1095" height="863" alt="image" src="https://github.com/user-attachments/assets/6a57c437-81eb-4510-852a-6e206c9c8587" />

# Step 4 - Configuring the Network Security Group (NSG) & Windows Firewall

### Network Security Group
From the resource group we navigate to "/.../-nsg" on the list.

As we want our VM to be accesible to the general public, we'll remove the default RDP rule and replacing it with one that is significantly more dangerous. 

<img width="1658" height="417" alt="image" src="https://github.com/user-attachments/assets/f982c9d1-d81b-4ca0-8b18-a244775596fb" />

In order to create a less secure inbound rule, we navigate to Settings --> Inbound Security rules --> + Add.

Settings:
- Source - Any
- Source Port Ranges - *
- Destination - Any
- Service - Custom
- Destination Port Ranges - *
- Protocol - Any
- Action - Allow
- Priority - 100
- Name - Dangerous_InBoundRule

This setup generates a high amount of warnings:

<img width="541" height="398" alt="image" src="https://github.com/user-attachments/assets/c04e1b2e-80a4-4bc0-96c6-fe97352a6510" />

### Windows Defender

In order to proceed, we need to RDP into the virtual machine. Since I'm running Windows 10 I only need to press the window key and enter RDP in the search bar.

<img width="331" height="97" alt="image" src="https://github.com/user-attachments/assets/9502d319-ed14-4160-96e6-f2e652afb593" />

We will then connect to the public IP-address that was assigned to the VM:
<img width="926" height="462" alt="image" src="https://github.com/user-attachments/assets/ef844694-f3a6-4525-a152-abb3cc6bb4cc" />

Enter the credentials for the VM and you're set. If you for whatever reason have lost or forgotten these credentials you can reset it by navigating to Virtual Machines --> (Virtual machine name) --> Help --> Reset password.

<img width="454" height="309" alt="image" src="https://github.com/user-attachments/assets/6f227673-adce-41e2-b026-3ebef56ca3db" />

Now the firewall needs to be configured so hackers can attempt to get access to our VM and generate some logs for us. We navigate

- Windows button -->
     - "wf.msc" -->
          - Windows Defender Firewall Properties -->
             - Firewall state "On" --> "Off"
                  - Apply --> OK

Naturally this generates a warning from Windows. We ping the public IP-address of the VM from our own personal computer to verify that it's exposed to the internet. 

<img width="441" height="122" alt="image" src="https://github.com/user-attachments/assets/2adcef25-fabe-4cdf-a7ee-fd154f5909f0" />

👾*Bug encounter: I could not ping the VM even after configuring the NGS and Windows Defender. I resolved it by doing the following command in Powershell in the VM:*

*netsh advfirewall firewall add rule name="ICMP Allow" protocol=icmpv4 dir=in action=allow*

*This allowed me to successfully ping the VM.*

# Step 5 - Event viewer, Sentinel and SIEM.

### Event Viewer

Since our VM is up and security configurations allow practically anyone with a computer to attempt to connect to our VM, we're already generating attempts at this point of the lab. Now we need a SIEM solution to collect these log and present them in a way that's readable for us to process.

First we'll confirm that we're feasible logs by generating a few ourselves.

<img width="444" height="258" alt="image" src="https://github.com/user-attachments/assets/0396243e-f612-4a7f-9e9d-9a8528245d4d" />

The name will allow us to easily find the logs, and then we'll just do a few attempts with incorrect passwords to generate failed logons. Afterwards we'll proceed to the VM and view Event Viewer --> Windows Logs --> Security.

<img width="1348" height="815" alt="image" src="https://github.com/user-attachments/assets/e48bdb92-886d-4524-92aa-95d5822f9022" />

We can extract some information from our failed login attempt:

<img width="809" height="1056" alt="image" src="https://github.com/user-attachments/assets/b4828cbd-74d5-41bc-8170-ce960dfa5559" />


1. The username that attempted to log into the device.
2. Description of the failure reason.
3. Name of the computer, the public IP-address and port used, both concealed for this exercise.
4. Time of date of the event.
5. Category and keyword of the incidents.
6. The computer (or this case, VM) that access was attempted towards.

We're also able to recognize that **Event ID 4648** means a successfull login attempt and **Event ID 4625** means a failed login attempt. Knowing this we can filter out the noise and find relevant logs manually if we so desire.

### Azure Sentinel
Leaving our VM open, we begin navigating through Microsft azure and reach Log Analytics workspaces (LAW) and create one. Again it's important that we select the correct resource group and region:

<img width="719" height="315" alt="image" src="https://github.com/user-attachments/assets/d3266a42-bea0-46f9-9485-a63789b67af2" />


We then proceed to create a Microsoft Sentinel to link to our LAW:

<img width="1630" height="479" alt="image" src="https://github.com/user-attachments/assets/b670dfb0-dc12-458b-8634-83f795aee462" />

Previously we would've navigated to the Content Hub in Microsoft Azure, but this feature has been moved to Microsoft Defender. The features we're after are the same but visually it might appear slightly different than other guides:

<img width="856" height="584" alt="image" src="https://github.com/user-attachments/assets/c9fae0f4-af9d-4809-bc4a-fde7666beb25" />

The program we're after is called _Windows Security Events_ and can be found by inputting "Security Event" in the search bar, we'll proceed with installing it:

<img width="1215" height="639" alt="image" src="https://github.com/user-attachments/assets/97ab2cb6-b0ff-4030-b6db-5832a3065981" />

After successfully installing it we can notice that we have no events. This is due to there not being a data connector to our VM which means there's no event to record. We'll configure it so that our VM will be able to output events for us:

- Windows Security Events --> Manage.
     - Windows Security Events via AMA --> Click box.
          - Open connector page
               - Create data collection rule, ensure right subscription and resource group.
                   - Provide the VM as a machine
            
<img width="568" height="305" alt="image" src="https://github.com/user-attachments/assets/7f5a57e4-d2e2-4afd-befe-8408eb69856d" />


<img width="532" height="208" alt="image" src="https://github.com/user-attachments/assets/2a4c0ef2-fe76-4d66-9206-68dd40e94b45" />



Once this is done, our VM will begin outputting logs for us to analyze. At this point of the lab we'll leave the VM open to allow logs to generate overtime.


# Step 6 - Analyzing a few logs.
After waiting for 24 hours, we can review the logs that were generated while our VM was up and vulnurable.

### KQL-queries
The first is to filter out the noise so we can see the logs we're interested in - Real attacks that users have made towards our VM. We'll use KQL language to devise a proper query:


SecurityEvent

| where EventID == 4625

| project TimeGenerated, Account, Computer, EventID, Activity, IpAddress


With this query we'll see every log pertaining to failed login attempts and we'll see a neat presentation of the details we listed in it.

<img width="873" height="617" alt="image" src="https://github.com/user-attachments/assets/0ee195fb-b843-4513-9405-a7e391ee0b3b" />


### Mapping our results
While the list is interesting, it can present even more useful information for us. We'll begin by downloading [GeoIP-Summarized](https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view) 

With the file on our computer we'll navigate back to Microsoft Sentinel --> (Name of the sentinel) --> Configuraton --> Watchlist. Note that this has also been moved to Microsoft Defender.

We'll create a new Watch list:

<img width="832" height="232" alt="image" src="https://github.com/user-attachments/assets/a7b848e2-0edd-4f0f-86c8-092196a76935" />

<img width="810" height="479" alt="image" src="https://github.com/user-attachments/assets/617cefa2-f7b5-4dd0-9fd8-67b5f4fc0d3e" />

Lastly we'll activate the watchlist by ticking in the box:

<img width="564" height="367" alt="image" src="https://github.com/user-attachments/assets/e3b1b897-3a68-460e-a655-92a1033177b1" />

Since GeoIP is large it will take some time before it will be added into the watchlist. When it's completed we should see roughly 55 thousands items being watched:

<img width="306" height="133" alt="image" src="https://github.com/user-attachments/assets/24b15a0c-043e-43f9-8598-ba7458adc5dc" />

Now we incorporate it into our KQL-query:


let GeoIPDB_FULL = _GetWatchlist("GeoIP");

let WindowsEvents = SecurityEvent

   | where EventID == 4625
   
   | order by TimeGenerated desc
   
   | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
   
WindowsEvents

| project TimeGenerated, Computer, AttackerIp = IpAddress, cityname, countryname, latitude, longitude

And this query gives us a better summary than our previous simple one:

<img width="1376" height="772" alt="image" src="https://github.com/user-attachments/assets/1cbcb70b-9cd2-46ac-a420-9b444ff21a65" />


### Workbook & Dashboard
While we can view our logs neatly through KQL queries, it's useful to be able to review them collectively and visually. To do so we need to navigate towards workbooks through our LAW:

- Log Analytics workspaces
     - (Name of our LAW)
          - Monitoring
               - Workbooks
We'll create a new workbook and delete the existing query items:

<img width="1888" height="629" alt="image" src="https://github.com/user-attachments/assets/e20289fa-f4d8-4409-b87a-c40ea6ce3947" />

Afterwards we'll click "Add" and select "Add Query". Import the following:

let GeoIPDB_FULL = _GetWatchlist("GeoIP");


SecurityEvent

| where EventID == 4625

| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)

| where isnotempty(latitude) and isnotempty(longitude)

| extend Latitude = todouble(latitude),
         Longitude = todouble(longitude)
         
| summarize FailureCount = count()
    by IpAddress, Latitude, Longitude, cityname, countryname

| extend countryname = strcat(cityname, " (", countryname, ")")

| project FailureCount, Latitude, Longitude, countryname

We'll set the visualization to Map:

<img width="1066" height="683" alt="image" src="https://github.com/user-attachments/assets/175788c6-da97-4f07-972e-43540a06f796" />

And lastly change "Metric Label" to countryname:

<img width="1089" height="757" alt="image" src="https://github.com/user-attachments/assets/c3bcd302-7973-4ba8-911a-e2eaf0ef4a34" />

We'll save and review our dashboard!

### The Final Product

<img width="1034" height="371" alt="image" src="https://github.com/user-attachments/assets/f49da33f-c27c-4bbf-ac7b-6c00ebdee95b" />


We have completed the lab and can now provide the following:

- Login attempts by users on our VM which includes
- Information such as IP-addresses, countries, name of the device used etc.
- Visualize these attempts:
     - As structured logs with summarized information.
     - Visual map indicating the geographical locations with highest attempts.
- Utilized several of Microsoft tools:
     - Created a resource group, VMnet, Windows VM, LAW, Sentinel and NSG.
     - Encountered and solved several bugs due to outdated guidelines and continous update of the Microsoft landscape.

# What I've learned. 📝

