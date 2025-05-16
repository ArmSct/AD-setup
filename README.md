![68747470733a2f2f692e696d6775722e636f6d2f705535413538532e706e67](https://github.com/user-attachments/assets/32c6eebe-e61f-4713-8be0-060416194b88)


# Installing Active Directory on Azure
This project demonstrates how to deploy and configure a basic Active Directory environment in Microsoft Azure, while capturing and analyzing network traffic using Wireshark. The lab includes setting up domain services, creating organizational units and users, joining client machines to the domain, and configuring Remote Desktop access for non-administrative users.

---

## Technology Utilized
- Microsoft Azure (VM deployment & networking)
- Windows Server & Windows 10
- Ubuntu Server
- Active Directory Domain Services (AD DS)
- RDP (Remote Desktop Protocol)
- PowerShell

---

## ⚙️ Setup and Configuration

### 1. Deploy Virtual Machines in Azure
Create a new resource group in the Azure Portal to host network-related resources. Within this group, create two virtual machines, a Windows server and a client. Both of these VMs will use the same resource group and Vnet.

![firefox_0kUyhqb1OF](https://github.com/user-attachments/assets/87abb90d-eb88-46b1-98a4-b59257c64bf4)

Once the VMs are made check for a connection between the domain controller and the client. The firewall settings must be configured on the domain controller so the ping request from the cleint won't time out.

![mstsc_BdutgAP0IC](https://github.com/user-attachments/assets/372c5a79-f2b6-4aab-b7e4-615f2ace7583)

Log into the client with RDP. Open the Command Prompt and ping the domain controller's IP address.
![mstsc_apcwwbkm69](https://github.com/user-attachments/assets/d41f68f6-c4c1-43e3-b417-9286314fc9c7)

---

### 2. Install and Configure Active Directory
Login to the domain controller and install the Active Directory Domain Services role. 

![mstsc_TdV3xpx1YJ](https://github.com/user-attachments/assets/2fcf7bd8-c5f0-48f9-a5f1-70ea446af3e8)


Once installed, the server is promoted to a Domain Controller by setting up a new domain name. After the server restarts, you log back in using the domain credentials (`mydomain.com\labuser`).

![mstsc_vcZk0YOgX0](https://github.com/user-attachments/assets/400be63c-1488-44c6-9276-0569e60c639c)

---

### 3. Create a Domain Admin User and a Normal User Account

Inside the Active Directory Users and Computers (ADUC) console, create two Organizational Units (OUs) named `_EMPLOYEES` and `_ADMINS`. A new user named Jane Doe is then created with the username `jane_admin` and a secure password. This user is added to the "Domain Admins" security group. You log out and then log back in as `mydomain.com\jane_admin`.

![mstsc_fyEm01VpjN](https://github.com/user-attachments/assets/8d256c7b-ebdf-41f4-962f-db1e70dd1d17)

![mstsc_KNDDPWf8z2](https://github.com/user-attachments/assets/b031ffbd-fb7f-4673-a6ad-1d1d1e3caf21)

![mstsc_UbFjZjdczU](https://github.com/user-attachments/assets/2baff2cd-9f9a-4847-9108-352b24eabc26)


---

### 4. Join Client-1 to the domain (mydomain.com)

Goto the Azure portal and navigate the client VM and for the DNS server add the domain controller's private IP address. Login into Client-1 as the original local admin (not jane_admin) and join it to the domain. The Create an OU named “_CLIENTS” and add Client-1 to the OU.

![mstsc_puvzNEyrtk](https://github.com/user-attachments/assets/531cc45a-28ef-4ad8-bb70-d1ddf901d747)

![mstsc_lUojA7QSN1](https://github.com/user-attachments/assets/76b68510-9758-46a1-8fe9-2accdf781ef9)


---

### 5. Enable Remote Desktop for Domain Users

You begin the second part of the lab by ensuring both DC-1 and Client-1 are running in the Azure Portal. After logging into Client-1 as `mydomain.com\jane_admin`, you open the System Properties and navigate to the Remote Desktop settings. Here, you allow members of the "Domain Users" group to access the machine via Remote Desktop. This setup allows any domain user to log into Client-1 remotely, though in a production environment this would typically be done through Group Policy.

---

### Create Multiple Users via PowerShell

Next, you log back into DC-1 as `jane_admin` and open PowerShell ISE with administrative privileges. A PowerShell script is created and run to automate the generation of multiple user accounts, all placed within the `_EMPLOYEES` OU. Once the script completes, you verify that the accounts appear in Active Directory Users and Computers.

---

### Test Logins with New Users

To confirm proper setup, you attempt to log into Client-1 using one of the newly created user accounts. As long as Remote Desktop access has been properly granted, the login should succeed. Be sure to use the password defined in the PowerShell script when signing in.
