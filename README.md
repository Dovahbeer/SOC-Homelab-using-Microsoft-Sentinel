# Azure-Sentinel-Homelab
### STATUS: 🔴 In Progress
### Disclaimer
As this is one of my earlier projects there will be parts that addresses even the most basic fundemental concepts. This ensures that I've managed to adequately understand and explain each step of the process for myself and hopefully clearly enough for you!

# Step 1 - Setting up the Virtual Machines
To create an enviorment that's also budget friendly, I decided to use VirtualBox with the following setup:

- VirtualBox version 7.2.8 with all custom features installed.

### Windows VM
- Windows version: Win11_25H2_English_x64_v2.
- Base memory set to 2048MB, number of CPU set to 2 and disk space set to 40GB.
- The ISO file was downloaded from [Microsoft's own website](https://www.microsoft.com/en-us/software-download/windows11).

  
### Kali Linux VM
- Kali Linux version: kali-linux-2026.1-virtualbox-amd64.
- Base memory set to 2048MB, number of CPU set to 2 and disk space set to 80GB.
- The VirtualBox version was downloaded from [Kali's own website](https://www.kali.org/get-kali/#kali-virtual-machines)

### Setting up the network
We want to create a network for the virtual machines to communicate on but also have it separate from our own private network. I devised the following setup:

**Network**: Internal Network.

**Name**: VMnet.
- **Windows VM**:
  - Adapter 1:
    - Ip-address:  192.168.20.10
    - Subnet mask: 255.255.255.0

- **Kali Linux VM**:
  - Adapter 1:
    - Ip-address:  192.168.20.20
    - Subnet mask: 255.255.255.0

💡 *In a real design it's better to adjust the subnet mask to the amount of ip-addresses that are necessary, but in a homelab /24 will be sufficient enough for our purposes.*
