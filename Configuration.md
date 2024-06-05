# IEEE 802.1X Configuration Guide

## 1. RADIUS Server Setup: Windows Server 2019 + NPS + AD

### a. Install NPS Role
- **Open Server Manager** > **Add Roles and Features**
- Select **"Network Policy and Access Services"** > **"Network Policy Server"**
- Click **Install**

### b. Register NPS in AD
- **Open NPS** > Right-click **NPS (Local)** > **"Register server in Active Directory"**
- Click **OK**

### c. Add Trusted Certificate
- **Purchase from GoDaddy** (e.g., "gd_bundle-g2-g1.crt")
- **Open MMC** > Add Snap-in > Certificates > Computer Account
- Right-click **Personal** > **All Tasks** > **Import** > **Next** > **Browse** > Select **.crt** file > **Next** > **Finish**

### d. Add APs as RADIUS Clients
- In **NPS**, navigate to **RADIUS Clients and Servers** > **RADIUS Clients** > **Add**
- Enter **Name**: **"MerakiAP1"**, **Address**: **"192.168.1.10"**, **Shared Secret**: **"MerakiSecret123"**
- Repeat for each AP (e.g., **MerakiAP2**: **192.168.1.11**, **MerakiAP3**: **192.168.1.12**)

### e. Configure PEAP-MSCHAPv2 Policy
- In **NPS** > **Policies** > **Network Policies** > **Add**
- **Name**: **"Meraki-WPA2-Enterprise"**, **Type**: **"Secure Wireless Connections"**
- **Conditions**: **NAS-Port-Type** = **"Wireless - IEEE 802.11"**, **Client IPv4** = **"192.168.1.0/24"**
- **Authentication Methods**: Microsoft: **Protected EAP (PEAP)**
- **PEAP Settings**: **Server Certificate** = **"GoDaddy_G2_G1"**, **EAP type** = **MSCHAPv2**
- **Set Policy Order**: Move this policy to the top

### f. (Optional) Deploy PEAP Profile via GPO
- On **Domain Controller**, open **Group Policy Management**
- Create **GPO**: **"Meraki-WPA2-Enterprise-Profile"**
- Edit **GPO** > **Computer Configuration** > **Windows Settings** > **Security Settings** > **Public Key Policies** > **Wireless Network (IEEE 801.X) Policies**
- Right-click > **Create New Windows Vista Policy** > **Name**: **"MerakiWPA2"**
- **Add** > **Infrastructure** > **Connection tab**: **Profile Name** = **"MerakiWPA2"**, **SSID** = **"MerakiEnterprise"**
- **Security tab**: **Authentication** = **WPA2-Enterprise**, **Encryption** = **AES**, **Network Authentication** = **PEAP**
- Link **GPO** to domain or OU

---

## 2. Dashboard Configuration: Meraki Cloud Controller

### a. Configure SSID
- Navigate to **Wireless** > **Configure** > **SSIDs** > **Create New SSID**
- **Name**: **"MerakiEnterprise"**, **VLAN Tagging**: **"Corporate VLAN (10)"**
- Navigate to **Wireless** > **Configure** > **Access control**
- Select **SSID**: **"MerakiEnterprise"**
- **Security**: **"Enterprise with my RADIUS server"**

### b. Add RADIUS Server
- Click **"Add server"**
- **Host IP**: **"192.168.1.100"** (NPS Server IP)
- **Auth port**: **1812**
- **Secret**: **"MerakiSecret123"** (match NPS)
- Click **Save**

### c. Configure VLAN Tagging (Optional)
- Navigate to **Wireless** > **Configure** > **Access control** > **VLAN tagging**
- Select **"MerakiEnterprise"** SSID
- Choose **"Dynamic VLAN assignment via RADIUS"**

### d. Test RADIUS
- Navigate to **Wireless** > **Configure** > **Access control**
- Under **RADIUS servers**, click **"Test"**
- Enter **Username**: **"john@domain.com"**, **Password**: **"P@ssw0rd123"**
- Click **"Begin test"**

### e. Set Up RADIUS Accounting
- Navigate to **Wireless** > **Configure** > **Access control**
- Select **"MerakiEnterprise"** SSID
- Under **RADIUS accounting servers**, click **"Add a server"**
- **Host IP**: **"192.168.1.100"**, **Auth port**: **1813**, **Secret**: **"MerakiSecret123"**
- Click **Done** > **Save changes**

---

## 3. Configuring WPA2-Enterprise with Cisco ISE 3.1

### a. Install Server Certificates
- **Purchase certificate from VeriSign** (e.g., "verisign_g5.p7b")
- In **ISE**, go to **Administration** > **System** > **Certificates** > **System Certificates**
- Click **Import** > Select **.p7b** file > **Submit**

### b. Add APs as Network Devices
- Go to **Administration** > **Network Resources** > **Network Devices** > **Add**
- **Name**: **"MerakiAP1"**, **IP Address**: **"192.168.1.10"**
- Check **Authentication Settings** > **Shared Secret**: **"MerakiSecret123"**
- Repeat for each AP (**MerakiAP2**: **192.168.1.11**, etc.)
- Click **Submit**

### c. Enable Policy Sets
- Go to **Administration** > **System** > **Deployment** > **Settings** > **Policy Sets**
- Click **Default policy** > **Create Above**
- **Name**: **"Meraki-WPA2"**, **Description**: **"WPA2 for Meraki APs"**
- **Condition**: **Device:Model EQUALS All Device Types**
- Click **Submit** > Log out and back in

### d. Configure Authentication Policy
- In **ISE**, select **Actions menu** > **Insert New Rule Above**
- **Name**: **"EAP-MSCHAPv2"**
- Click **Conditions icon** > **Create New Condition**
- **Network Access** > **EAP Authentication EQUALS EAP-MSCHAPv2**
- **Use**: Select **Active Directory** as identity store
- Go to **Administration** > **Identity Management** > **External Identity Sources** > **Active Directory**
- **Join Domain**: **"domain.com"**, **Username**: **"administrator"**, **Password**: **"P@ssw0rd123"**

---

## 4. Additional Software and Tools

### a. Certificate Authorities
- **GoDaddy** (Windows NPS example)
- **VeriSign** (Cisco ISE example)

### b. Network Analysis
- **Wireshark** (version 3.6.2) for capturing 802.1X/EAPOL frames

### c. RADIUS Testing Tools
- **NTRadPing** (version 1.5) for Windows
- **radtest** (part of FreeRADIUS) for Linux

### d. Group Policy Management
- **GPMC** (built into Windows Server 2019)

### e. Certificate Management
- **Microsoft Management Console (MMC)** with Certificates snap-in

### f. Active Directory Management
- **Active Directory Users and Computers (ADUC)**

---

This comprehensive guide covers the complete configuration steps using real-world examples with **Windows Server 2019 (NPS + AD)** and **Cisco ISE 3.1**. It also lists additional software tools that can be used for certificate management, network analysis, and RADIUS testing. By following these steps, you can successfully set up a secure **WPA2-Enterprise** environment using **RADIUS** authentication.
