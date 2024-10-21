<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)
## High-Level Deployment Steps ️

These steps provide a roadmap for deploying Active Directory within Azure Virtual Machines.

1.  <a href="#section1">**Install Active Directory Domain Services:**</a><a href="#section1"></a>
2.  <a href="#section2">**Promote Server to Domain Controller**</a>
3.  <a href="#section3">**Create Domain Admin User and OUs**</a>
4.  <a href="#section4">**Join Client Machine to Domain, Configure Remote Desktop**</a>
5. ‍‍ <a href="#section5">(Optional) **Create Additional Users with Script**</a>  

<h2>Detailed Deployment and Configuration Steps</h2>

Here's a breakdown of each step involved in the deployment process:

<section id="section1">
<h3>Step 1: Install Active Directory Domain Services</h3>




1. Access your Azure VM (DC-1) using Remote Desktop.
2. Open Server Manager and install the **Active Directory Domain Services** role.

![image](https://github.com/user-attachments/assets/e696c25f-f238-4cfe-bc29-9747238152bd)

</section>
<section id="section2">
<h3>Step 2: Promote Server to Domain Controller</h3>


1. Within Server Manager, select **Promote this server to domain controller**.
   ![image](https://github.com/user-attachments/assets/2ae37368-01c5-4305-a797-72c508bd3baf)
3. Choose **Add a new forest** and enter your desired domain name (e.g., `mydomain.com`).
   ![image](https://github.com/user-attachments/assets/95f38a32-c6e3-4999-8ac2-423fe2729a04)
5. Set a strong password for the Directory Services Restore Mode (DSRM) account.
   ![image](https://github.com/user-attachments/assets/1c65d447-16f7-4db4-add3-0ddf6c037b77)
7. Review the configuration summary and click **Next** to proceed.
8. The server will restart. Log back in using the newly created domain name (e.g., `mydomain.com\labuser`).
</section>
<section id="section3">
<h3>Step 3: Create Domain Admin User and OUs</h3>


1. Launch **Active Directory Users and Computers (ADUC)**.
<br>![image](https://github.com/user-attachments/assets/5d319c5f-8374-4c61-b964-f13c8dcd44fc)

3. Create two Organizational Units (OUs):
    * `_EMPLOYEES` for user accounts.
    * `_ADMINS` for administrative accounts.
<br>![image](https://github.com/user-attachments/assets/d46d9fa6-2c0b-41ea-9add-30d54877140e)

4. Create a new user named "Jane Doe" with the username `jane_admin` and a strong password (replace `Cyberlab123!` with a strong password).
<br>![image](https://github.com/user-attachments/assets/c8a7fb80-c607-442f-8b29-4a2069653b0a)

6. Add "jane_admin" to the **Domain Admins** security group.
![image](https://github.com/user-attachments/assets/ca239898-240e-444b-bd62-2eb5d35399a2)

8. Log out and log back in as `mydomain.com\jane_admin`.
</section
<section id="section4"></section>
<h3>Step 4: Join Client Machine to Domain, Configure Remote Desktop</h3>

1. Log in to Client-1 with the local administrator account (e.g., `labuser`).
2. Join Client-1 to the domain named `mydomain.com` (replace with your chosen name).
![image](https://github.com/user-attachments/assets/c457ce0a-0472-47a5-8352-6e5e67491731)

4. Verify Client-1 shows up in ADUC on the Domain Controller.
5. Create a new OU named `_CLIENTS` and move Client-1 into it.
6. Log in to the Domain Controller as `mydomain.com\jane_admin`.
7. Open System Properties on the Domain Controller.
8. Go to the **Remote Desktop** tab and allow **Domain Users** access.

<section id="section5">
<h3>Step 5: Create Additional Users with Script (Optional)</h3>
</section>
<ol>
  <li>On the Domain Controller, open PowerShell ISE as an administrator.</li>
  <li>Create a new file and paste the following script content (replace placeholders with your desired values)</li>
</ol>
```powershell
# ... (Paste your PowerShell script here)
<pre>
<b>PowerShell</b>

$number_of_users = 10  # Adjust the number of users to create
$ou_name = "_EMPLOYEES"  # Change OU name if needed
$base_username = "user"
$domain_name = "mydomain.com"  # Replace with your domain name
$starting_number = 1

for ($i = 0; $i -lt $number_of_users; $i++) {
  $username = "$base_username" + ($starting_number + $i)
  $password = ConvertTo-SecureString "Cyberlab123!" -AsPlainText -Force
  $new_user = New-ADUser -Name $username -SamAccountName $username -Password $password -Path "OU=$ou_name,$domain_name"
  if ($new_user) {
    Write-Host "User '$username' created successfully."
  } else {
    Write-Error "Failed to create user '$username'."
  }
}
</pre>
![image](https://github.com/user-attachments/assets/9f76f5ba-ff89-4acd-b792-9f9224e09c5a)

<h3>This will result in 10,000 randomized names to be generated</h3>
